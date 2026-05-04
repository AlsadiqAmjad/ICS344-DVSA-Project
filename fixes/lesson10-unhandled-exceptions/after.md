# After Fix

The function validates required values before using them:

```python
orderId = event.get("orderId")
userId = event.get("user")

if not orderId:
    return {"status": "err", "msg": "missing required order id"}

if not userId:
    return {"status": "err", "msg": "missing required user"}
```
