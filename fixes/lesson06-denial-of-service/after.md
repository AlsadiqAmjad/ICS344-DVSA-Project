# After Fix

API Gateway stage throttling was configured:

```text
Rate: 5 requests/second
Burst: 10 requests
```

Excess traffic now returns HTTP 429 before overloading the backend.
