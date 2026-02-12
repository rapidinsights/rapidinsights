# Slowly Changing Dimensions (SCD)

When dimension attributes change over time, decide how to handle history. This decision is **per-attribute**, not per-dimension — a single dimension table can mix SCD types.

## Type 0: Retain Original

Never update the attribute. Value from first creation is preserved forever.

**Use for:** Original assignment values, attributes that should not change (date of birth, original sales rep).

## Type 1: Overwrite

Replace old value with new. No history preserved.

**Use for:** Corrections to data entry errors, attributes where history is not analytically relevant (fixing a misspelled customer name).

**Trade-off:** Simple, but all historical facts associate with the current attribute value. If a customer moved from Toronto to Vancouver, all historical orders appear to be from Vancouver.

## Type 2: Add New Row

Insert a new row with updated attribute value, a new surrogate key, and effective date columns (`valid_from`, `valid_to`, `is_current`). The old row remains with its original surrogate key.

**Use for:** Any attribute where you need to analyze facts in the context of what was true at the time — customer segment, pricing tier, employee department, product category.

**Implementation pattern:**

```
surrogate_key  |  natural_key  |  city       |  valid_from  |  valid_to    |  is_current
1001           |  CUST-42      |  Toronto    |  2023-01-01  |  2024-06-15  |  false
1002           |  CUST-42      |  Vancouver  |  2024-06-15  |  9999-12-31  |  true
```

Fact tables join to the surrogate key, so historical facts retain point-in-time context automatically.

## Type 3: Add New Column

Add a `previous_` column alongside the current column. Only stores one level of history.

**Use sparingly:** Only when the business explicitly needs "before and after" analysis for a single, planned change (e.g., reorganization needing `current_region` and `previous_region`).

## Practical Guidance

For most SMB consulting engagements, start with **Type 1 everywhere** (overwrite) and upgrade specific attributes to **Type 2 only when the client demonstrates an analytical need** for point-in-time accuracy. Type 2 adds real complexity (snapshot logic, surrogate key management, join patterns) and most SMB clients don't need it initially.
