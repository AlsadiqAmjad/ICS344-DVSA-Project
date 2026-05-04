# API Gateway Throttling - Lesson 06

Applied at:

```text
API Gateway -> DVSA-APIS -> Stages -> Stage
```

Configuration used in the report:

```text
Rate: 5 requests per second
Burst: 10 requests
```

Expected verification:

- Pre-fix: repeated requests reach backend and produce server errors.
- Post-fix: excessive requests return HTTP 429 Too Many Requests.
