# Fact Table Patterns

## Transaction Fact Table

One row per discrete event at the atomic grain. Default starting point.

**When to use:** The business process generates individual, independent events — orders placed, invoices sent, tickets created.

**Characteristics:** Sparse (rows only exist when events happen), naturally additive, grows append-only.

## Periodic Snapshot Fact Table

One row per entity per time period (daily, weekly, monthly). Captures state at regular intervals.

**When to use:** Track levels, balances, or cumulative quantities over time — inventory levels, account balances, pipeline value, backlog size.

**Characteristics:** Dense (every entity gets a row every period, even if nothing changed), semi-additive facts (don't sum across time), requires a scheduled snapshot process.

## Accumulating Snapshot Fact Table

One row per process instance with a defined beginning and end, with multiple date columns marking milestones. Rows are updated as the process progresses.

**When to use:** The business process has a predictable lifecycle with measurable stages — order-to-delivery, application-to-approval, lead-to-close.

**Characteristics:** One row per process instance, multiple date FKs (one per milestone), dates start null and fill in as milestones are reached, includes lag/duration facts between milestones.

**Extremely valuable for manufacturing/fulfillment operations.** Example milestones:

```
order_created -> ordered -> inventory_received -> production_complete -> shipped -> delivered
```

Each milestone gets a date FK. Derive cycle time facts (days from order to inventory, days from inventory to completion, etc.).

## Factless Fact Table

A fact table with no numeric facts — only dimension FKs. Records event occurrence or many-to-many relationships.

**When to use:** Tracking coverage, eligibility, attendance, or associations. Examples: which promotions were available on which dates, which students attended which classes, which products were eligible for which discounts.
