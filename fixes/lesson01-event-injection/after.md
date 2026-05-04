# After Fix

The request body is parsed as plain JSON and headers are treated as a normal object:

```javascript
let req = {};
let headers = event.headers || {};
try {
  req = JSON.parse(event.body || "{}");
} catch (e) {
  return callback(null, {
    statusCode: 400,
    headers: { "Access-Control-Allow-Origin": "*" },
    body: JSON.stringify({ status: "err", msg: "invalid json" })
  });
}
```
