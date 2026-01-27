# Overview

Clients are getting their account blocked after entering wrong password only one time. The bug is on the attempt counter.

# Diagnostic steps
- Reproduce locally with detailed logs: record every read/write of the counter with timestamp and origin.
- Check logs across layers including API gateway, auth service, cache, and database.
- Look for non‑atomic operations: read followed by write without locking.
- Run concurrency tests with multiple threads or processes simulating simultaneous attempts.
- Confirm policy parameters: max attempts, reset window, and lockout duration.

# Fixing the code in Python
Principles: use atomic operations (Redis INCR with EXPIRE), differentiate keys by origin, and reset the counter on successful authentication.
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

Example without Redis using database with optimistic locking
# Pseudocode using SQL transaction
BEGIN TRANSACTION;
SELECT attempts, window_start FROM auth_failures WHERE user_id = ? FOR UPDATE;
# update attempts and window_start atomically
COMMIT;

Important: always reset the counter on success by calling reset_attempts immediately after successful authentication to avoid residual locks.

# Tests and monitoring
- Load tests to validate behavior under concurrency.
- Alerts for spikes in account locks by user or origin.
- Structured logs with request_id to trace the full flow.

# Risks and final recommendations
- Do not disable lockout; tune parameters to balance security and usability.
- Use origin-specific keys to prevent one service from causing global lockouts.
- Audit changes in production and keep a rollback plan ready.
