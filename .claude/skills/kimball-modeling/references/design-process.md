# The Four-Step Design Process

Every new business entity or process follows this sequence. Do not skip steps or reorder them.

## Step 1: Select the Business Process

A business process is a measurable operational activity — not a department, not a report, not a KPI. It generates events.

**Identify it:** Ask "What happens repeatedly in your business that you want to measure?" The answer is a verb phrase: *receiving orders, shipping products, processing invoices, onboarding employees, closing tickets.*

**Rules:**

- Model one business process per fact table. Never combine "orders" and "returns" into one fact table just because they share columns.
- Prioritize the process with the highest business impact and most available data. For SMBs coming from spreadsheets, this is almost always the revenue-generating transaction.
- A single source system often contains multiple business processes.

**Deliverable:** *"We are modeling the [business process] as captured by [source system]."*

## Step 2: Declare the Grain

The grain is the level of detail in each row of the fact table. This is the most important decision and the one most often skipped.

**Declare it:** Write: *"One row represents one [thing]."*

- "One row represents one order" (transaction grain)
- "One row represents one order line item" (finer transaction grain)
- "One row represents one order's daily status snapshot" (periodic snapshot grain)
- "One row represents one order status change event" (accumulating snapshot grain)

**Rules:**

- The grain must be atomic — the finest level of detail captured by the source system.
- Every fact and every dimension FK must be consistent with the declared grain. If even one column doesn't make sense at the stated grain, either the grain is wrong or the column doesn't belong.
- Never mix grains in a single fact table.
- When in doubt, go more granular. Order-line-item grain is almost always better than order grain.

**Deliverable:** *"Each row in fct_orders represents one customer order at the time of creation."*

## Step 3: Identify the Dimensions

Dimensions are the "by" in every business question: revenue *by customer*, orders *by month*, shipments *by region*.

**Identify them:** For each declared grain, ask: "How does the business want to filter, group, or slice this fact?"

### Standard Dimension Checklist

Apply to every process:

| Dimension | What It Captures | Notes |
|-----------|------------------|-------|
| **Date** | When the event occurred | Every fact table gets at least one. Often multiple: order_date, ship_date, due_date. Use a shared `dim_date` with surrogate key. |
| **Customer/Account** | Who is involved | May need SCD if attributes change over time. |
| **Product/Item** | What was transacted | In manufacturing/apparel, might be garment style, type, or SKU. |
| **Location/Geography** | Where it happened | Shipping address, warehouse, store. Can be own dimension or embedded in customer. |
| **Employee/Owner** | Who handled it internally | Sales rep, account manager. Enables performance analysis. |
| **Status/Stage** | Current state | Often a junk dimension or embedded flag set. Status is time-variant — be cautious. |
| **Channel/Source** | How it arrived | Web, retail, partner. Critical for acquisition analysis. |

**Rules:**

- Dimensions should be wide and descriptive. A good dimension has 20-50+ columns of textual attributes.
- Prefer single-valued dimensions. For many-to-many relationships, use a bridge table.
- Every dimension needs a surrogate key (integer or hash). Never use operational natural keys as PKs.
- Include the natural/business key as an attribute alongside the surrogate key.

**Deliverable:** A dimension list with each dimension's name, business key, and 3-5 key attributes.

## Step 4: Identify the Facts

Facts are the numeric measurements of the business process at the declared grain.

### Three Types of Facts

| Type | Definition | Example | Rules |
|------|-----------|---------|-------|
| **Additive** | Can be summed across all dimensions | Revenue, quantity | Most useful. Prioritize these. |
| **Semi-additive** | Can be summed across some dimensions but not time | Account balance, inventory level | Use snapshots. |
| **Non-additive** | Cannot be meaningfully summed | Unit price, ratio, percentage | Store components (numerator + denominator) so ratio can be recalculated after aggregation. |

**Rules:**

- Facts must be numeric and measurable. Text is a dimension attribute or degenerate dimension.
- Facts must be consistent with the grain.
- Store facts at the lowest granularity. Store `unit_price` and `quantity` separately.
- Null facts are acceptable and preferable to zero. Null = "not applicable"; zero = "measured, value was zero."
- **Degenerate dimensions** (order number, invoice number, PO number) live directly in the fact table because they don't warrant their own dimension table. Used for operational drill-through.

**Deliverable:** A fact list specifying each measure's name, type (additive/semi-additive/non-additive), unit, and nullability.
