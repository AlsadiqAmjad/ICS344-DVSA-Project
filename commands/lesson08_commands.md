# Lesson 08 - Logic Vulnerabilities

> This is a race-condition / workflow sequencing test in your own DVSA order flow.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export TOKEN="<user-jwt>"
export ORDER_ID="<order-id>"
```

## Optional get-order check

Some DVSA versions require `isAdmin` as a string. Use this format if checking the order before the test:

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"get","order-id":"'"$ORDER_ID"'","isAdmin":"false"}' | jq
```

## Pre-fix race sequence

Submit billing and then immediately update the same order:

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"billing","order-id":"'"$ORDER_ID"'","data":{"ccn":"4242424242424242","exp":"10/2030","cvv":"123"}}'

curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"update","order-id":"'"$ORDER_ID"'","items":{"1020":5}}'
```

Expected pre-fix evidence:

```text
The update returns status ok / cart updated.
The final confirmation still shows $25.
DynamoDB shows item 1020 quantity 5 and totalAmount 25.
```

## Fix code pattern

Vulnerable pattern:

```python
if response["Item"]["orderStatus"] > 110:
    res = {"status": "err", "msg": "order already paid"}
    return res
```

Fixed pattern used in the project:

```python
order = response["Item"]

if order.get("orderStatus", 0) >= 110:
    return {"status": "err", "msg": "order cannot be updated after billing started"}
```

## Post-fix verification

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN" \
  --data '{"action":"update","order-id":"'"$ORDER_ID"'","items":{"1020":5}}' | jq
```

Expected result:

```json
{
  "status": "err",
  "msg": "order cannot be updated after billing started"
}
```
