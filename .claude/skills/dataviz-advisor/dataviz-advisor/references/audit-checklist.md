# Visualization Audit Checklist

Score each item Pass / Fail / N/A. Summarize findings grouped by severity (Critical > Warning > Suggestion).

---

## 1. Audience Alignment

- [ ] Target audience is identified (executive, operational, technical)
- [ ] Chart complexity matches audience sophistication
- [ ] Information density is appropriate (exec: 4-6 charts, ops: 6-10, max 5-7 per view)
- [ ] Narrative framework is evident (SCR, What/So What/Now What, or Knaflic 6-step)

## 2. Chart Type Selection

- [ ] Chart type matches the analytical relationship (comparison, trend, distribution, part-to-whole, correlation)
- [ ] No pie charts with >3 slices
- [ ] No dual Y-axes (use small multiples or indexed charts instead)
- [ ] No gratuitous 3D effects
- [ ] Tables used only when exact values matter more than pattern
- [ ] Max 4-5 data series per chart

## 3. Titles & Labels

- [ ] Titles are insight-driven, not descriptive ("Revenue up 12% QoQ" not "Revenue by Quarter")
- [ ] Title sequence test: reading only titles tells the complete story
- [ ] All axes have labels with units
- [ ] Numbers formatted for readability (1.2M not 1,200,000; thousands separators)
- [ ] Decimals limited to needed precision
- [ ] Natural language date labels where appropriate ("Q3 2025" not "2025-07-01")

## 4. Context & Comparisons

- [ ] Every KPI has a comparison point (vs. target, prior period, or benchmark)
- [ ] Direction indicators use color + icon together (never color alone)
- [ ] Reference lines for targets/averages where relevant
- [ ] Annotations at inflection points explaining what happened
- [ ] Data freshness indicator ("Last updated: ...")

## 5. KPI Card Design (if applicable)

- [ ] Answers 3 questions instantly: What's the number? Good or bad? Trending which way?
- [ ] 3-6 KPI cards maximum in hero section
- [ ] Size hierarchy: headline value largest, metric name medium, context smallest
- [ ] Sans-serif font with tabular figures for numbers
- [ ] Sparkline present for recent trend

## 6. Layout & Hierarchy

- [ ] Most important information in upper-left quadrant (F/Z-pattern scanning)
- [ ] Top row = status KPIs, middle = trends, bottom = detail
- [ ] Progressive disclosure: default view answers the most common question
- [ ] Sufficient white space; not every pixel filled
- [ ] Consistent spacing between elements
- [ ] Breadcrumb navigation in drill-throughs (if multi-level)

## 7. Color & Accessibility

- [ ] 4-8 colors maximum in data palette
- [ ] Color used for emphasis only; grey as default
- [ ] Consistent color mapping across all charts (same entity = same color everywhere)
- [ ] Not relying on color alone to convey meaning (shapes, patterns, text labels paired)
- [ ] Minimum 4.5:1 contrast ratio (WCAG)
- [ ] Works for color-vision-deficient users (blue-orange preferred over red-green)

## 8. Scale & Accuracy

- [ ] Bar charts start at zero
- [ ] Line charts clearly labeled if Y-axis is truncated
- [ ] Consistent scales across panels showing comparable data
- [ ] Lie Factor ~1 (graphic effect proportional to data effect)
- [ ] No misleading area/volume encodings

## 9. Anti-Pattern Check

- [ ] No chart junk (gratuitous gridlines, borders, background images, decorations)
- [ ] No vanity metrics (totals without rates, ratios, or context)
- [ ] No information overload (>7 items competing for attention)
- [ ] No rainbow color palette
- [ ] No mismatched title vs. chart content (title claims X, chart shows Y)

## 10. Code Quality (source code audit only)

- [ ] Chart configuration separated from business logic
- [ ] Color palette defined centrally (not hardcoded per chart)
- [ ] Responsive/mobile handling present
- [ ] Data transformations occur before the visualization layer (not inline)
- [ ] Consistent chart styling (theme/template applied)

---

## Severity Definitions

- **Critical**: Misleading data (wrong scale, lie factor >1.5, missing context that changes interpretation). Fix immediately.
- **Warning**: Poor practice that degrades understanding (chart junk, wrong chart type, color-only encoding). Fix soon.
- **Suggestion**: Polish and refinement (font consistency, spacing, label formatting). Fix when convenient.
