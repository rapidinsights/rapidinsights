---
name: dataviz-advisor
description: "Guide for creating effective data visualizations and auditing dashboards/charts for best practices. Follows a stakeholder storytelling playbook grounded in Knaflic, Tufte, Few, and Dykes. Use when the user wants to: create a data visualization, chart, dashboard, or KPI card; audit or review an existing dashboard, plot, or report for best practices; choose the right chart type for their data; or tailor a data deliverable to a specific audience (executive, operational, technical). Supports both screenshot-based visual audits and source code reviews."
---

# DataViz Advisor

Two workflows: **Create** and **Audit**. Both use the playbook in [references/playbook.md](references/playbook.md) as the authoritative source. Read that file before starting either workflow.

## Create Workflow

Use when helping build a new visualization, dashboard, or KPI card.

1. **Identify audience and intent.** Ask: Who will see this? What decision should it drive? Map to executive, operational, or technical audience per playbook section 4.
2. **Choose narrative framework.** Executive presentations -> SCR. Operational reports -> What/So What/Now What. Comprehensive analyses -> Knaflic 6-step. (Playbook section 1.)
3. **Select chart type.** Ask "What relationship am I showing?" Use the chart selection table in playbook section 3. Flag any cautioned chart types (pie >3 slices, dual-axis, 3D).
4. **Apply formatting rules.** Insight-driven titles (action title technique), proper number formatting, axis labels, color discipline (4-8 colors max, grey default, accent for emphasis). Sections 2 and 5.
5. **Review against anti-patterns.** Check playbook section 6 before delivering. Every metric needs context. Every chart needs a "so what."

When generating code, apply these principles regardless of library. Produce clean, well-commented chart configuration.

## Audit Workflow

Use when reviewing an existing dashboard, chart, or report. Supports two input modes:

### Screenshot Audit (visual review)
When the user provides a screenshot or image:
1. Read [references/audit-checklist.md](references/audit-checklist.md) and [references/playbook.md](references/playbook.md).
2. Identify the apparent target audience.
3. Score each checklist section (Pass/Fail/N/A). Skip section 10 (code quality).
4. Group findings by severity: Critical > Warning > Suggestion.
5. Provide specific, actionable recommendations with playbook references.

### Code Audit (implementation review)
When the user provides source code:
1. Read [references/audit-checklist.md](references/audit-checklist.md) and [references/playbook.md](references/playbook.md).
2. Read the source code. Identify chart types, titles, color usage, layout, and data transformations.
3. Score all 10 checklist sections including section 10 (code quality).
4. Group findings by severity: Critical > Warning > Suggestion.
5. Provide specific code-level fixes alongside design recommendations.

## Quick Reference

**Chart selection shortcut** (playbook section 3):
- Comparison -> bar chart (horizontal if long labels)
- Trend -> line chart (max 4-5 series)
- Part-to-whole -> stacked bar (max 4-5 segments)
- Distribution -> histogram or box plot
- Correlation -> scatter plot
- Before/after -> slope chart
- Exact values -> table

**KPI card 5-second test** (playbook section 2): What's the number? Good or bad? Trending which way?

**Title test**: Read only titles in sequence. If they tell the complete story, the dashboard works.
