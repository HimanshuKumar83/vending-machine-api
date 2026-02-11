# Bugs Found

## Bug 1 – Slot Code Format Not Validated

Endpoint:
POST /slots

Issue:
API accepts invalid slot codes like "A1,223,!=4"

Expected:
Slot code should follow format like A1 or B2

Actual:
Any string accepted

Fix:
Added regex validation for slot code format

## Bug 2 – Item Price Allowed to be Zero

**Endpoint:**  
POST /slots/{slot_id}/items  
POST /slots/{slot_id}/items/bulk

**Issue:**  
Item price validation allowed price = 0.

Schema used:

```python
price: int = Field(..., ge=0)..


Bug 3:


**Endpoint:**  
POST /slots/{slot_id}/items

**Issue:**  
Per-slot capacity validation used incorrect comparison logic.

Original logic:

```python
if slot.current_item_count + data.quantity < settings.MAX_ITEMS_PER_SLOT:
    raise ValueError("capacity_exceeded").