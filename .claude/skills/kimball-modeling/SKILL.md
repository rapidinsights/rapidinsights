---
name: kimball-modeling
description: Guide for designing and auditing Kimball-style dimensional models (star schemas, fact tables, dimension tables). Use when the user wants to create a dimensional model for a table or dataset, audit existing models for best practices, design fact or dimension tables, choose grain, identify SCDs, build a bus matrix, or optimize data models in a pipeline. Triggers on requests like "create a model for my orders table", "help me audit my models", "I want to create a dimensional model", "how should I model this data", or "optimize my data models".
---

# Kimball Dimensional Modeling

Practitioner's guide for designing and auditing Kimball-style dimensional models.

## Workflow Decision Tree

Determine the task, then follow the appropriate path:

**"Create a dimensional model"** -> Follow the [Four-Step Design Process](references/design-process.md)
1. Select the business process
2. Declare the grain
3. Identify the dimensions
4. Identify the facts

**"What fact table pattern should I use?"** -> See [Fact Table Patterns](references/fact-patterns.md)
- Transaction (default) — one row per discrete event
- Periodic snapshot — one row per entity per time period
- Accumulating snapshot — one row per process lifecycle
- Factless — event occurrence or many-to-many associations

**"How should I handle changing dimensions?"** -> See [SCD Reference](references/scd.md)
- Default to Type 1 (overwrite) for most attributes
- Upgrade to Type 2 (add new row) only when point-in-time analysis is needed

**"Help me with special dimension patterns"** -> See [Dimensions Reference](references/dimensions.md)
- Date dimension, conformed dimensions, junk dimensions, role-playing dimensions, bus matrix

**"Audit my existing model"** -> Use the [Audit Checklist](#audit-checklist) below, with detailed anti-patterns in [Anti-Patterns Reference](references/anti-patterns.md)

**"Plan an engagement end-to-end"** -> See [Engagement Playbook](references/engagement-playbook.md) for Discovery -> Design -> Implementation -> Validation phases

## Core Principles

- **One business process per fact table.** Never combine different processes.
- **Declare the grain first.** Write: "One row represents one [thing]." Every column must be consistent with this statement.
- **Go atomic.** Store the finest grain the source system provides. Aggregate in marts, not fact tables.
- **Surrogate keys on all dimensions.** Never use operational/natural keys as PKs.
- **Wide dimensions.** A good dimension has 20-50+ columns of descriptive attributes.
- **Facts are numeric.** Text belongs in dimensions or as degenerate dimensions.
- **Store components, not ratios.** Keep numerator and denominator so ratios can be recalculated at any aggregation level.
- **Conformed dimensions enable cross-process analysis.** Share `dim_date`, `dim_customer`, `dim_product` across all fact tables.

## Naming Conventions

| Object | Prefix | Example |
|--------|--------|---------|
| Staging model | `stg_` | `stg_nomos_orders` |
| Intermediate model | `int_` | `int_orders_enriched` |
| Dimension table | `dim_` | `dim_customer`, `dim_date` |
| Fact table | `fct_` | `fct_orders`, `fct_shipments` |
| Mart / reporting table | `mart_` | `mart_sales_summary` |
| Surrogate key | `_key` suffix | `customer_key`, `date_key` |
| Natural/business key | `_id` suffix | `customer_id`, `order_id` |
| Date FK in fact | `_date_key` | `order_date_key`, `ship_date_key` |

## Audit Checklist

Quick checklist for reviewing existing models. See [Anti-Patterns Reference](references/anti-patterns.md) for detailed explanations.

1. Grain statement exists and is documented
2. No mixed grains in any fact table
3. Surrogate keys on all dimensions
4. Natural keys preserved as attributes for traceability
5. Facts are numeric — no text in fact columns
6. Facts are consistent with the declared grain
7. Additive facts are truly additive across all dimensions
8. Semi-additive facts use snapshot tables
9. Non-additive facts store components, not just ratios
10. Conformed dimensions are shared (not duplicated)
11. Date dimension exists and is conformed
12. Degenerate dimensions live in the fact table
13. No One Big Table patterns
14. Referential integrity — all FKs resolve

## dbt Project Layout

```
models/
├── staging/           -- stg_{source}_{entity}.sql (clean, rename, cast)
├── intermediate/      -- int_{entity}_{transform}.sql (business logic)
├── dimensions/        -- dim_{entity}.sql (conformed, shared)
├── facts/             -- fct_{process}.sql (one per business process)
└── marts/             -- mart_{domain}_{use_case}.sql (wide, denormalized)
```

Build order: staging -> dimensions -> facts -> marts.
