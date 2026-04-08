# PAYVAULT  
**Payment API • Reference Documentation**  
**v2.4 • April 2026 • REST / JSON**

---

## Overview
The PayVault Payments API allows platforms to initiate, track, and manage money movement programmatically. All requests are made over HTTPS and return standard JSON responses. Authentication is performed via Bearer tokens issued through the PayVault dashboard.

This document covers the core payment initiation endpoint: `POST /v2/payments`.

---

## Base URL
https://api.payvault.io/v2

---

## Authentication
All API requests must include a valid API key in the Authorization header using the Bearer scheme.

Authorization: Bearer pvt_live_sk_...

> 🔒 Never expose your API key publicly.

---

## Create a Payment
**POST** `/v2/payments`

---

## Request Headers

| Parameter        | Type   | Required | Description |
|-----------------|--------|----------|------------|
| Authorization   | string | Yes      | Bearer token |
| Content-Type    | string | Yes      | application/json |
| Idempotency-Key | string | No       | UUID v4 |
| X-Api-Version   | string | No       | API version |

---

## Request Body Parameters

| Parameter    | Type    | Required | Description |
|-------------|--------|----------|------------|
| amount      | integer | Yes | Amount in cents |
| currency    | string  | Yes | ISO 4217 |
| source      | object  | Yes | Source object |
| destination | object  | Yes | Destination object |

---

## Example Request

```python
import requests

url = "https://api.payvault.io/v2/payments"

headers = {
    "Authorization": "Bearer ...",
    "Content-Type": "application/json"
}

payload = {
    "amount": 2500,
    "currency": "USD"
}

requests.post(url, json=payload, headers=headers)
```

---

## Success Response

```json
{
  "id": "pay_123",
  "status": "pending"
}
```

---

## HTTP Status Codes

| Code | Description |
|------|------------|
| 201  | Created |
| 400  | Bad Request |
| 401  | Unauthorized |
| 500  | Server Error |

---

## Sandbox

Use test tokens like:

- tok_visa_success  
- tok_declined  

