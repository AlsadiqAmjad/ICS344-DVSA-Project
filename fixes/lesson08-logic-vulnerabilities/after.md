# After Fix

The update path rejects changes once billing has started:

```python
order = response["Item"]
if order.get("orderStatus", 0) >= 110:
    return {"status": "err", "msg": "order cannot be updated after billing started"}
```
