# Data Communication & Stakeholder Storytelling Playbook

> The authoritative reference for all visualization decisions. Read this file when creating or auditing any data deliverable.

---

## Table of Contents
- [1. Narrative Structure](#1-narrative-structure--always-lead-with-the-insight)
- [2. KPI / Metric Card Design](#2-kpi--metric-card-design--the-5-second-rule)
- [3. Choosing the Right Visualization](#3-choosing-the-right-visualization)
- [4. Tailoring to Your Audience](#4-tailoring-to-your-audience)
- [5. Dashboard Layout Principles](#5-dashboard-layout-principles)
- [6. Anti-Patterns](#6-anti-patterns--what-to-avoid)
- [7. Essential Reading & Resources](#7-essential-reading--resources)

---

## 1. Narrative Structure — Always Lead with the Insight

Never open with methodology. Choose one framework based on context:

**SCR (Situation -> Complication -> Resolution)** — Best for executive presentations
- Situation: Indisputable shared context ("We targeted 15% growth")
- Complication: The tension ("Growth came in at 4%")
- Resolution: Your recommendation first, then supporting evidence
- Support with no more than 3 key arguments (MECE: no gaps, no overlaps)

**What -> So What -> Now What** — Best for operational updates & recurring reports
- What: The objective finding
- So What: The business impact
- Now What: Concrete next steps with owners and deadlines

**Knaflic's 6-Step Process** — Best for comprehensive analyses
1. Understand context (audience, desired action, medium -> distill into a one-sentence "Big Idea")
2. Choose an effective visual
3. Eliminate clutter
4. Focus attention (one accent color, grey for everything else)
5. Think like a designer (affordances, accessibility, aesthetics)
6. Tell a story (setup -> conflict -> resolution)

**Core principle:** Data alone informs. Narrative alone engages. Data + Narrative + Visuals together **drive change**. Stories are 10x more memorable than statistics alone (Dykes).

---

## 2. KPI / Metric Card Design — The 5-Second Rule

Every KPI card must answer 3 questions instantly: What's the number? Is it good or bad? Which way is it heading?

### Required Elements
| Element | Rule |
|---|---|
| **Metric name** | Clear, unambiguous. Spell out acronyms for mixed audiences |
| **Headline value** | Largest text on card. 24-36px bold, sans-serif, tabular (monospaced) figures |
| **Comparison** | % change or delta vs. benchmark/target/prior period. A number without context is useless |
| **Direction indicator** | Color + icon together (green up / red down). Never color alone |
| **Sparkline** | Small trend line showing recent trajectory |

### Formatting Rules
- Sans-serif fonts for numbers (Inter, Roboto, Lato, Source Sans Pro)
- Use **tabular figures** so digits align vertically
- Size hierarchy: primary value 24-36px -> metric name 16-18px -> context 12-14px
- Max **2 fonts, 3 weights, 4 font sizes** per dashboard
- Abbreviate large numbers: **1.2M** not 1,200,000
- Minimize decimals to only needed precision
- Always use thousands separators
- Include "Last updated" timestamp
- Tuck definitions behind a consistent info icon

### Layout Rules
- **3-6 KPI cards maximum** in the hero section (avoid "Christmas Tree Effect")
- Evenly distributed on a grid with consistent padding
- Light grey background behind white cards for natural separation
- Use tabs or drill-throughs for additional metrics — don't cram

---

## 3. Choosing the Right Visualization

**Always ask: "What relationship am I showing?"** — not "What looks interesting?"

### Chart Selection Quick Reference

| Relationship | Best Chart | Notes |
|---|---|---|
| **Comparison across categories** | Bar chart (vertical or horizontal) | Horizontal when labels are long or 10+ categories. Always sort by value unless natural order exists |
| **Trend over time** | Line chart | Max 4-5 lines. Highlight key line in color, grey the rest |
| **Part-to-whole** | Stacked bar chart | Max 4-5 segments. Most important segment at baseline |
| **Distribution** | Histogram or box plot | Histogram for single variable; box plot for comparing groups |
| **Relationship (2 variables)** | Scatter plot | Warn viewers: correlation != causation |
| **Comparison at 2 time points** | Slope chart | Remarkably effective for before/after |
| **Exact values needed** | Table | Use when precision matters more than pattern |

### Decision Resources
- **Andrew Abela's Chart Chooser** — single-page flowchart (comparison / composition / distribution / relationship)
- **FT Visual Vocabulary** — 9 categories, open-source on GitHub
- **From-data-to-viz.com** — interactive decision tree
- **Knaflic's test:** "Whatever will be easiest for your audience to read"

### Charts to Use with Extreme Caution
- **Pie charts** -> Only with 2-3 clearly different slices. Humans compare bar length far better than wedge angle. 5+ categories -> use horizontal bar
- **Dual-axis charts** -> Creator can make any two series appear correlated by scaling axes. Use small multiples or indexed charts instead
- **3D charts** -> Never use gratuitous 3D. Distorts perception universally

---

## 4. Tailoring to Your Audience

### Executive Audiences
- 3-5 slides max for core message
- Lead with the recommendation
- Simple, familiar chart types only
- Methodology in appendix (ready for Q&A)
- **Inverted pyramid:** "Are we on track?" -> "Why?" -> "Show me more"

### Operational Managers
- Connect insights to levers they control
- Show trend data for planning
- Focus on "what to do differently Monday morning"
- Dashboards with drill-down, regular cadence (weekly/monthly)

### Technical Audiences
- Include methodology, sample sizes, confidence intervals, caveats
- Precise terminology, reproducible documentation
- Simplification != dilution — structure so non-experts grasp essentials while experts see rigor

### The Action Title Technique (Highest-Leverage Change)
- Replace descriptive titles -> insight-driven titles
- Bad: "Revenue by Quarter"
- Good: "Revenue grew 15% QoQ, driven by new product launches"
- **Test:** Read only titles in sequence. If they tell the complete story, the deck works
- Quantify wherever possible, active voice, 10-15 words max

### Handling Pushback
- Pre-list top 3-5 expected objections with data-backed responses
- Present limitations honestly — transparency builds credibility
- "I don't trust the data" often means "I don't like what the data says"
- Interactive dashboards let stakeholders co-create insight (builds trust)
- If slide title claims one thing but chart shows another, trust evaporates instantly

---

## 5. Dashboard Layout Principles

### Information Hierarchy
- **Upper-left quadrant** = most important information (F-pattern and Z-pattern scanning)
- Top row: Status KPIs ("Are we good?")
- Middle: Supporting trends ("Why?")
- Bottom: Granular details ("Show me more")

### Progressive Disclosure
- Default view answers the most common question
- Interactions answer follow-ups
- Max 2-3 levels of drill-down (avoid "click fatigue")
- Include breadcrumb navigation in drill-throughs

### White Space & Layout
- White space reduces cognitive load — don't fill every pixel
- Light grey background + white cards = natural separation without heavy borders
- Consistent spacing between all elements
- Max 5-7 visualizations per view

### Color Discipline
- **4-8 colors maximum** in data palette
- Default to neutral/grey; reserve color for emphasis only
- Consistent color mapping across all charts (Product A = blue everywhere)
- **Never rely on color alone** — 8% of men have color vision deficiency
- Pair color with icons, text labels, patterns, or shapes
- Blue-orange as alternative to red-green
- Minimum 4.5:1 contrast ratio (WCAG)
- Test with Color Oracle or Coblis

### Mobile Considerations
- Multi-column -> single-column scrollable
- Touch targets >= 48x48 pixels
- Fewer metrics, larger text
- No horizontal scrolling

---

## 6. Anti-Patterns — What to Avoid

| Anti-Pattern | What It Is | Fix |
|---|---|---|
| **Chart junk** | Decorative elements with no information (gradients, 3D, background images) | Maximize data-ink ratio. Remove borders, use direct labels instead of legends |
| **Misleading scales** | Bar charts not starting at zero, inconsistent scales across panels | Bar charts ALWAYS start at zero. Line charts can truncate but label clearly |
| **Information overload** | 15+ charts on one screen. Humans hold ~7 items in working memory | 5-7 visualizations max per view. Max 5 data series per chart |
| **Lack of context** | "Conversion: 3.2%" with no benchmark, trend, or target | Every metric needs a frame: vs. target, vs. prior period, vs. industry |
| **Vanity metrics** | Numbers that look impressive but don't drive decisions (total pageviews, total users) | Use actionable metrics: rates, ratios, with clear cause-and-effect |
| **Color misuse** | Rainbow palettes, different color per bar in single-variable chart, implying causation | Consistent palette, color for emphasis only, explicit disclaimers on correlation |

**Tufte's Lie Factor:** Size of effect in graphic / Size of effect in data. >1 = exaggeration. <1 = understatement. Always aim for 1.

---

## 7. Essential Reading & Resources

| Resource | Author | Best For |
|---|---|---|
| *Storytelling with Data* | Cole Nussbaumer Knaflic | Narrative technique, the 6-step process |
| *Show Me the Numbers* / *Information Dashboard Design* | Stephen Few | Dashboard design rigor |
| *Effective Data Storytelling* | Brent Dykes | Data + narrative + visuals framework |
| *The Visual Display of Quantitative Information* | Edward Tufte | Foundational principles, data-ink ratio |
| *How Charts Lie* | Alberto Cairo | Critical visual literacy |
| *DataStory* | Nancy Duarte | Inspiring action through story |
| *The Pyramid Principle* | Barbara Minto | SCR framework, top-down communication |
| FT Visual Vocabulary | Financial Times | Chart selection (free, GitHub) |
| Andrew Abela's Chart Chooser | Andrew Abela | Quick chart type decision flowchart |
| From-data-to-viz.com | — | Interactive chart decision tree |
