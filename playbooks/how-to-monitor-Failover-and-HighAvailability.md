
# How to Monitor Failover & High Availability
**Google SRE-Aligned | AWS + Kubernetes + PostgreSQL + Redis Stack**

---

## 1. Objective

This playbook defines operational procedures for monitoring, validating, and responding to failover and high availability (HA) events using an observability platform aligned with **Google SRE principles**.

Primary goals:

- Maintain **Service Level Objectives (SLOs)**
- Minimize **Mean Time to Recovery (MTTR)**
- Ensure **error budget compliance**
- Provide reliable and automated failover mechanisms

---

## 2. System Architecture

### Stack Components

- **Cloud Provider**: AWS (Multi-AZ / Multi-Region)
- **Orchestration**: Kubernetes (EKS)
- **Database**: PostgreSQL (Primary + Read Replica with streaming replication)
- **Cache**: Redis (Cluster mode with Sentinel or managed ElastiCache failover)
- **Monitoring Stack**:
  - Prometheus (metrics)
  - Alertmanager (alerting)
  - Grafana (visualization)
  - OpenTelemetry (tracing)
  - Loki (logs)

---

## 3. SRE Foundations

### 3.1 SLIs (Service Level Indicators)

- Availability (% successful requests)
- Latency (p95 / p99)
- Error Rate
- Failover Success Rate
- Replication Lag

### 3.2 SLOs (Example)

| Metric | Target |
|-------|--------|
| Availability | 99.95% |
| Failover Time | < 30 seconds |
| Replication Lag | < 1 second |
| Error Rate | < 0.1% |

### 3.3 Error Budget

Error Budget = 0.05% downtime per month

Policies:
- Freeze deployments if exceeded
- Prioritize reliability work

---

## 4. Key Metrics

### Kubernetes
- pod_ready_status
- pod_restarts_total
- node_not_ready

### PostgreSQL
- replication_lag_seconds
- wal_sync_delay
- primary_node_status

### Redis
- replication_offset
- failover_events_total
- memory_fragmentation_ratio

### Infrastructure (AWS)
- ELB health checks
- EC2 status checks
- Network latency

---

## 5. Installation & Configuration

### 5.1 Deploy Monitoring Stack (Helm)

```bash
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack
```

### 5.2 Configure Prometheus Scraping

```yaml
scrape_configs:
  - job_name: 'kubernetes'
    kubernetes_sd_configs:
      - role: pod
```

### 5.3 PostgreSQL Exporter

```bash
kubectl apply -f postgres-exporter.yaml
```

### 5.4 Redis Exporter

```bash
kubectl apply -f redis-exporter.yaml
```

---

## 6. Alerting Strategy (SRE-Aligned)

### Principles

- Alerts must be **actionable**
- Avoid alert fatigue
- Page only on **user-impacting issues**

### Example Alerts

```yaml
- alert: HighErrorRate
  expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
  for: 2m
  labels:
    severity: critical

- alert: FailoverTooSlow
  expr: failover_duration_seconds > 30
  for: 1m
  labels:
    severity: critical

- alert: ReplicationLagHigh
  expr: replication_lag_seconds > 1
  for: 5m
  labels:
    severity: warning
```

---

## 7. Failover Workflow

### Automatic Failover (Expected Flow)

1. Health check fails (Kubernetes or AWS ELB)
2. Pod/node marked unhealthy
3. Traffic rerouted via Service / Load Balancer
4. PostgreSQL replica promoted
5. Redis Sentinel triggers failover
6. Monitoring system records event

---

## 8. Manual Failover (Runbook)

### PostgreSQL Failover

```bash
kubectl exec -it postgres-replica -- promote
```

### Redis Failover

```bash
redis-cli -p 26379 SENTINEL failover mymaster
```

### Kubernetes Node Drain

```bash
kubectl drain <node> --ignore-daemonsets
```

---

## 9. Incident Response (Google SRE Model)

### 9.1 Incident Lifecycle

1. Detection (alert fires)
2. Triage (assess severity)
3. Mitigation (restore service)
4. Resolution
5. Postmortem

---

### 9.2 Severity Classification

| Severity | Description |
|----------|------------|
| SEV-1 | Full outage |
| SEV-2 | Major degradation |
| SEV-3 | Minor issue |
| SEV-4 | Informational |

---

### 9.3 Response Actions

#### SEV-1
- Page on-call immediately
- Initiate failover
- Communicate status

#### SEV-2
- Investigate within SLA
- Prepare mitigation

---

## 10. Postmortem (Blameless)

Follow Google SRE guidelines:

- No blame assigned
- Focus on system failures
- Document:
  - Root cause
  - Detection gaps
  - Action items

---

## 11. Testing & Chaos Engineering

### Regular Tests

- Simulate node failure
- Simulate DB failover
- Inject network latency

### Tools

- Chaos Mesh
- LitmusChaos

---

## 12. Observability Best Practices

- Use RED method (Rate, Errors, Duration)
- Use USE method (Utilization, Saturation, Errors)
- Correlate logs + metrics + traces

---

## 13. Security

- IAM roles with least privilege
- TLS everywhere
- Secrets via Kubernetes Secrets / AWS Secrets Manager

---

## 14. Maintenance

- Weekly failover drills
- Monthly SLO review
- Continuous alert tuning

---

## 15. Key Commands

```bash
kubectl get pods
kubectl describe node
kubectl logs <pod>

redis-cli info replication
psql -c "SELECT * FROM pg_stat_replication;"
```

---

## 16. Summary

This playbook ensures:

- Reliable failover mechanisms
- SRE-aligned operations
- Scalable and observable infrastructure

---

**End of SRE Playbook**
