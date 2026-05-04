# Before Fix

`DVSA-ORDER-GET/get_order.py` directly indexed required fields:

```python
orderId = event["orderId"]
userId = event["user"]
```

Missing input raised `KeyError` and leaked a stack trace.
