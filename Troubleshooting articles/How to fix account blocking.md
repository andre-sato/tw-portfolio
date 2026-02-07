# Overview

Clients are getting their account blocked after entering wrong password only one time. The bug is on the attempt counter.

# Diagnostic steps
1. Reproduce locally with detailed logs: record every read/write of the counter with timestamp and origin.
1. Check logs across layers including API gateway, auth service, cache, and database.
1. Look for non‑atomic operations: read followed by write without locking.
1. Run concurrency tests with multiple threads or processes simulating simultaneous attempts.
1. Confirm policy parameters: max attempts, reset window, and lockout duration.

# Fixing the code in Python
Use atomic operations such as Redis INCR with EXPIRE, differentiate keys by origin, and reset the counter on successful authentication.
Example using Redis recommended for high concurrency
```sh
import redis
r = redis.Redis()

def record_failed_attempt(user_id, origin):
    key = f"auth:fail:{user_id}:{origin}"
    # INCR is atomic; set TTL on first attempt
    attempts = r.incr(key)
    if attempts == 1:
        r.expire(key, 300)  # 5 minute window
    return attempts

def reset_attempts(user_id, origin):
    key = f"auth:fail:{user_id}:{origin}"
    r.delete(key)

def is_locked(user_id, origin, max_attempts=5):
    key = f"auth:fail:{user_id}:{origin}"
    attempts = int(r.get(key) or 0)
    return attempts >= max_attempts
```

Important: always reset the counter on success by calling _reset_attempts_ immediately after successful authentication to avoid residual locks.

# Tests and monitoring
- Load tests to validate behavior under concurrency.
- Alerts for spikes in account locks by user or origin.
- Structured logs with request_id to trace the full flow.
