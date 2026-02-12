# Special Dimension Patterns

## Table of Contents

- [The Date Dimension](#the-date-dimension)
- [Conformed Dimensions](#conformed-dimensions)
- [Junk Dimensions](#junk-dimensions)
- [Role-Playing Dimensions](#role-playing-dimensions)
- [The Bus Matrix](#the-bus-matrix)

---

## The Date Dimension

Every dimensional model needs a shared date dimension. Build it once and reuse across every engagement.

### Minimum Attributes

```
date_key (integer, YYYYMMDD format)
full_date (date type)
day_of_week (Monday, Tuesday...)
day_of_week_number (1-7)
is_weekend (boolean)
week_of_year
month_number
month_name
quarter (Q1, Q2, Q3, Q4)
year
fiscal_year (if client uses non-calendar fiscal year)
fiscal_quarter
is_holiday (boolean — populate per client's country/region)
holiday_name
```

### Rules

- Use integer surrogate key in YYYYMMDD format (e.g., 20250212). Enables joins and human readability.
- Pre-populate 10+ years forward and backward.
- Special key `0` or `-1` represents "date unknown" or "not applicable." Every fact table date FK should default to this rather than null.
- Build one `dim_date` as a dbt seed or standalone model. Copy into every project.

---

## Conformed Dimensions

A dimension shared across multiple fact tables with consistent keys, attributes, and definitions.

**Why it matters:** If `fct_orders` and `fct_shipments` both join to the same `dim_customer`, you can drill across both fact tables by customer and get consistent results. Different customer definitions per fact table makes cross-process analysis impossible.

### Rules

- Conformed dimensions must share the same surrogate keys, natural keys, attribute columns, and attribute values across every fact table.
- The date dimension is always conformed.
- Customer, product, and employee dimensions should be conformed when the same entities appear across multiple processes.
- Conforming dimensions is the single most important thing for enterprise-wide analytics.

**Practical approach:** Identify the 3-5 dimensions that span multiple processes early. Build them as standalone dbt models in a `dimensions/` directory. Have every fact table reference the same dimension model.

---

## Junk Dimensions

Groups together low-cardinality flags and indicators that don't belong in any real dimension.

**When to use:** Your fact table has several boolean or small-enum columns (is_rush, contains_glitter, has_pocket_print, is_retail, is_replacement, is_split) that are descriptive but don't warrant individual dimensions.

**Implementation:** Create a dimension table with one row per observed combination of flags. Assign a surrogate key. Fact table holds only the single FK instead of 6+ boolean columns.

**Benefits:** Cleaner fact table, enables group-by on flag combinations, reduces fact table width. Observed combinations are far fewer than the theoretical maximum.

**When to skip:** If you only have 1-2 flags, leave them in the fact table. Junk dimensions shine at 4+ low-cardinality attributes.

---

## Role-Playing Dimensions

A single dimension table referenced multiple times in the same fact table, each playing a different "role."

**Canonical example:** `dim_date` joined three times to `fct_orders` — as `order_date`, `ship_date`, and `due_date`.

**Implementation in dbt:** Create the base dimension once, reference with aliased joins:

```sql
select
  o.order_id,
  order_dates.month_name as order_month,
  ship_dates.month_name as ship_month,
  due_dates.month_name as due_month
from fct_orders o
left join dim_date order_dates on o.order_date_key = order_dates.date_key
left join dim_date ship_dates on o.ship_date_key = ship_dates.date_key
left join dim_date due_dates on o.due_date_key = due_dates.date_key
```

Do not duplicate the dimension table.

---

## The Bus Matrix

A planning tool mapping business processes (rows) to dimensions (columns), showing shared dimensions across processes.

### How to Build

|  | dim_date | dim_customer | dim_product | dim_location | dim_workspace | dim_owner |
|--|----------|-------------|-------------|-------------|--------------|----------|
| **Order Placement** | X | X | X | X | X | X |
| **Inventory Receiving** | X |  | X |  | X |  |
| **Order Fulfillment** | X | X | X | X | X | X |
| **Shipping** | X | X |  | X |  |  |

### Rules

- Build in the first working session with the client. It becomes the project roadmap.
- Each row becomes a fact table. Each column becomes a dimension.
- Checkmarks show conformed dimensions across processes.
- Prioritize the row with the most checkmarks first — it delivers the most analytical surface area.

**Deliverable:** A filled bus matrix shared with the client. Both a technical design doc and a communication tool non-technical stakeholders can read.
