# Engagement Playbook

How to apply Kimball dimensional modeling to a new client or business entity.

## Phase 1: Discovery (1-2 hours)

1. **List all source systems.** Spreadsheets, apps, databases, SaaS exports. Don't model yet — just catalog.
2. **Identify business processes.** For each source, ask: "What happens here? What events does this capture?" Write each as a verb phrase.
3. **Build the bus matrix.** Map processes to dimensions. Forces the client to articulate how their business entities relate.
4. **Prioritize.** Pick the one process with the highest business impact and best data availability. This becomes the first sprint.

## Phase 2: Design (2-4 hours)

5. **Declare the grain.** Write the grain statement. Get client sign-off.
6. **Draft dimensions.** For each, list business key and 5-10 key attributes. Focus on what the business calls things.
7. **Draft facts.** List every numeric measurement at the declared grain. Classify as additive, semi-additive, or non-additive.
8. **Identify degenerate dimensions.** Order numbers, PO numbers, invoice numbers — things that live in the fact table.
9. **Decide SCD types.** For each dimension attribute: Type 1 (overwrite) or Type 2 (track history)? Default to Type 1 unless explicit need.

## Phase 3: Implementation (dbt project structure)

```
models/
├── staging/
│   └── stg_{source}_{entity}.sql       -- Clean, rename, cast, deduplicate
├── intermediate/
│   └── int_{entity}_{transform}.sql     -- Business logic, joins, calculations
├── dimensions/
│   ├── dim_date.sql                     -- Conformed, shared across all facts
│   ├── dim_customer.sql
│   ├── dim_product.sql
│   └── dim_{entity}.sql
├── facts/
│   ├── fct_{process}.sql                -- One per business process
│   └── fct_{process}_snapshot.sql       -- If periodic/accumulating pattern needed
└── marts/
    └── mart_{domain}_{use_case}.sql     -- Wide, denormalized for BI consumption
```

10. **Build staging models first.** One per source table. These do nothing but clean.
11. **Build dimensions next.** Conformed dimensions before process-specific ones.
12. **Build fact tables last.** They reference the dimensions.
13. **Build marts on top.** Pre-joined wide tables for specific dashboards or user groups.

## Phase 4: Validation

14. **Grain test:** Query the fact table grouped by its primary key. If duplicates exist, the grain is violated.
15. **Referential integrity test:** Every FK in the fact table should join to exactly one dimension row.
16. **Additivity test:** Sum a key fact across all dimensions. Does the total make business sense and match the source system?
17. **Bus matrix test:** Can you join two fact tables through a conformed dimension and get sensible results?
