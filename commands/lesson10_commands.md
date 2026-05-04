# Lesson 10 - Unhandled Exceptions

> This lesson uses a malformed request to prove error leakage, then validates that the function returns a safe message after input validation.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export TOKEN="<user-jwt>"
```

## Pre-fix test

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"get"}' | jq
```

Expected vulnerable behavior:

```json
{
  "errorMessage": "'orderId'",
  "errorType": "KeyError",
  "stackTrace": [
    "File \"/var/task/get_order.py\", line 30, in lambda_handler",
    "orderId = event[\"orderId\"]"
  ]
}
```

## Vulnerable code pattern

```python
orderId = event["orderId"]
userId = event["user"]
is_admin = json.loads(event.get("isAdmin", "false").lower())
```

## Fixed code pattern

```python
orderId = event.get("orderId")
userId = event.get("user")

if not orderId:
    return {"status": "err", "msg": "missing required order id"}

if not userId:
    return {"status": "err", "msg": "missing required user"}

is_admin = json.loads(str(event.get("isAdmin", "false")).lower())
```

## Post-fix verification

Run the same malformed request:

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"get"}' | jq
```

Expected fixed behavior:

```json
{
  "status": "err",
  "msg": "missing required order id"
}
```

No `stackTrace`, `KeyError`, `/var/task/get_order.py`, or source-code line should appear in the client response.
