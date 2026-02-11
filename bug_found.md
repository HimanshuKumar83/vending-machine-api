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

Endpoint:  
POST /slots/{slot_id}/items  
POST /slots/{slot_id}/items/bulk

Issue:  
Item price validation allowed price = 0.

Schema used:

price: int = Field(..., ge=0)

Expected:  
Price must be greater than 0 as per specification.

Actual:  
API accepted items with price = 0.

Fix Applied:  
Changed validation from ge=0 to gt=0.

---

## Bug 3 – Incorrect Slot Capacity Validation Logic

Endpoint:  
POST /slots/{slot_id}/items

Issue:  
Per-slot capacity validation used incorrect comparison logic.

Original logic:

if slot.current_item_count + data.quantity < settings.MAX_ITEMS_PER_SLOT:
    raise ValueError("capacity_exceeded")

This rejected valid additions even when under capacity.

Expected:  
Items should only be rejected when exceeding limits.

Actual:  
Valid quantities were rejected.

Fix Applied:

if slot.current_item_count + data.quantity > settings.MAX_ITEMS_PER_SLOT:
    raise ValueError("capacity_exceeded")

## Bug 4 – Bulk Add Ignores Slot Capacity

Endpoint:
POST /slots/{slot_id}/items/bulk

Issue:
Bulk add API does not validate slot capacity before adding items.

Expected:
Total quantity added in bulk should not exceed slot capacity.

Actual:
API allows adding items beyond slot capacity.

Proof:
Slot capacity = 5  
Bulk request added quantities totaling more than 5  
API still returned success.

Cause:
bulk_add_items() does not check:
- slot capacity
- MAX_ITEMS_PER_SLOT
- current_item_count update

Result:
Slot can overflow beyond defined capacity.
---
## Bug 5 – No Denomination Validation

Endpoint:
POST /purchase
GET /purchase/change-breakdown

Issue:
System does not validate whether inserted cash follows supported denominations.

Example:
User can insert unrealistic values like 3 INR.

Expected:
Cash should match supported denominations or valid combinations.

Actual:
Any integer is accepted.

Impact:
Breaks real-world vending machine logic.

Fix Suggestion:
Validate cash against SUPPORTED_DENOMINATIONS or accept list of coins.