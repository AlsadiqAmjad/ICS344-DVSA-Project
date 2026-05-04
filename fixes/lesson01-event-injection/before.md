# Before Fix

`DVSA-ORDER-MANAGER/order-manager.js` used `node-serialize` to parse user-controlled input:

```javascript
const serialize = require('node-serialize');
var req = serialize.unserialize(event.body);
var headers = serialize.unserialize(event.headers);
```

This allowed serialized function payloads to execute during deserialization.
