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