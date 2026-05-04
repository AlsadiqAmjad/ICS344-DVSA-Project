# Before Fix

`DVSA-ORDER-UPDATE/update_order.py` did not strictly block updates once billing started.

```python
if response["Item"]["orderStatus"] > 110:
    return {"status": "err", "msg": "order already paid"}
```
