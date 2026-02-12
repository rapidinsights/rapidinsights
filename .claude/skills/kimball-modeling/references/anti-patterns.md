# Anti-Patterns Quick Reference

Use this checklist when auditing existing dimensional models.

| Anti-Pattern | Why It's Wrong | What to Do Instead |
|-------------|----------------|-------------------|
| Mixing grains in one fact table | Produces incorrect aggregations; users will sum things that shouldn't be summed | Separate fact tables per grain |
| Using operational keys as PKs | Breaks when source system changes keys, makes SCD impossible | Surrogate keys everywhere |
| Putting text in fact tables | Can't aggregate text; bloats the fact table | Move to a dimension or degenerate dimension |
| Building dimensions with only key and name | Starves the model of analytical power | Wide dimensions with rich attributes (20-50+ columns) |
| Pre-aggregating facts | Loses granularity permanently | Store atomic facts; aggregate in marts |
| One Big Table for everything | No reusability, metric definitions scatter, impossible to extend | Star schema with clear fact/dimension separation |
| Skipping the grain declaration | Every downstream decision becomes ambiguous | Write the grain statement first, always |
| Storing computed ratios without components | Can't re-aggregate correctly at different grains | Store numerator and denominator separately |
| Null FKs instead of unknown member row | Requires outer joins everywhere, complicates queries | Use a default "unknown" dimension row (key = 0 or -1) |
| Snowflaking dimensions unnecessarily | Adds join complexity without analytical benefit | Flatten into the dimension unless there's a strong reason |
| Overusing Type 2 SCD | Adds pipeline complexity most SMB clients don't need | Start with Type 1; upgrade to Type 2 only when point-in-time analysis is needed |
| Combining multiple processes in one fact | Sparse rows, confused grain, ambiguous metrics | One fact table per business process |

## Audit Checklist

When reviewing an existing model, verify each of these:

1. **Grain statement exists** and is documented
2. **No mixed grains** — every row in a fact table represents the same thing
3. **Surrogate keys** on all dimensions (not natural/operational keys)
4. **Natural keys preserved** as dimension attributes for traceability
5. **Facts are numeric** — no text in fact columns
6. **Facts are consistent with grain** — no line-item facts in an order-grain table
7. **Additive facts are truly additive** across all dimensions
8. **Semi-additive facts** are in snapshot tables with appropriate aggregation logic
9. **Non-additive facts** store components, not just the ratio
10. **Conformed dimensions** are shared across fact tables (not duplicated)
11. **Date dimension** exists and is conformed
12. **Degenerate dimensions** (order numbers, etc.) are in the fact table, not in separate tables
13. **No One Big Table** patterns — fact and dimension separation is maintained
14. **Referential integrity** — all FKs resolve to dimension rows
