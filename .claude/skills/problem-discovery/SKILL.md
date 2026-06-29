---
name: problem-discovery
description: Use when facing an ambiguous, high-stakes, or unfamiliar problem and tempted to jump to a solution, fix, or implementation — before designing or coding, when the real problem, its root cause, or what "solved" means is not yet pinned down. Covers diagnosis, scoping a vague request, root-cause work, and knowing when you understand enough to act.
---

# Problem Discovery

## Overview

The work you do *before* attempting a solution: gather the relevant context, and
understand **which context matters most**. The goal is to **converge** — know
when to stop — rather than sprawl (gather forever).

**Core principle: hold the problem space open.** Most bad solutions come from
collapsing the problem and solution spaces too early — you latch onto a fix, then
retrofit the problem to justify it. Keep two spaces strictly separate:

- **Problem space** — what is true, what is wrong, why, and what "solved" means.
- **Solution space** — how to fix it. *Off-limits during discovery.*

A capable model already has good instincts here — it questions premises and
measures before acting. This skill exists to make that instinct **rigorous and
convergent**, and to stop the solution plan from leaking out before discovery is
actually done.

## When to use

- A symptom is visible but the cause or mechanism is not pinned down.
- "Just tell me how to fix X" under delivery pressure, with X underspecified.
- High blast radius — a wrong *problem definition* is expensive to discover late.
- NOT for: a well-understood task with a known fix, or pure execution.

## The discovery deliverable is NOT a solution plan

Discovery produces a **converged understanding of the problem** plus a handoff —
never a "here's what to build" plan. The moment you are writing *Phase 1: do X* or
*the fix is…*, you have left the problem space. Note the idea, set it aside, and
get back to discovery.

## The spine (a loop, not a waterfall)

```
frame → decompose → ledger → hypotheses → evidence → stress → converge → hand off
                       ▲                                │
                       └────────────── loop ────────────┘
```

The middle is a loop: hypotheses drive evidence-gathering, evidence updates the
ledger, the updated ledger reshapes the hypotheses — repeat until convergence.

| # | Stage | Produces | The one binding rule |
|---|---|---|---|
| 1 | **Frame** | The problem as a *statement*, not a task | Separate observable symptom from suspected cause; name who has it and what "solved" looks like |
| 2 | **Decompose** | A MECE issue tree | Pick the split axis deliberately (algebraic > process > dichotomy > borrowed); diagnostic branches only |
| 3 | **Ledger** | Every claim sorted: fact / inference / assumption / unknown | No assumption may wear the costume of a fact; weight facts by source quality |
| 4 | **Hypotheses** | ≥2 competing, falsifiable, **diagnostic-only** | Each paired with "what evidence would kill this?" — a hypothesis about a *fix* is the solution space leaking in |
| 5 | **Evidence** | Next observation, chosen by **information gain** | Importance = discriminating power between live hypotheses, not raw relevance or impact |
| 6 | **Stress** | Attacks on the framing | Inversion ("what makes this the *wrong* problem?") + pre-mortem, *before* trusting your understanding |
| 7 | **Converge** | The stop signal | Stop when more context wouldn't change your understanding — not when you've collected everything |
| 8 | **Hand off** | A decision brief about the *problem* | Present top-down (Pyramid + SCQA); carry the live ledger and kill conditions forward |

The two layers that make discovery converge instead of sprawl:

- **Assumption** — a passive belief you are carrying (lives in the ledger).
- **Hypothesis** — an assumption you have committed to testing (runs the engine).

Promotion between them is the heartbeat. **Full method, with worked detail for
each stage: see [framework.md](framework.md).**

## Red flags — you have collapsed into the solution space

- Writing "Phase 1: do X" or "the fix is…" while still in discovery.
- One explanation on the table instead of ≥2 competing ones.
- A hypothesis about a *fix* ("add an index") instead of a *cause* ("it's lock contention, not I/O").
- Ranking what to investigate by gut or impact instead of which evidence kills a rival.
- "I already know what this is" — that is an *assumption*; promote it to a falsifiable hypothesis and test it.
- Gathering more context with no stopping criterion in mind.

## Common mistakes

| Mistake | Fix |
|---|---|
| A priority-ordered list of "likely causes" | That is neither MECE nor competing hypotheses. Build the tree; carry rivals; score evidence by discrimination. |
| A measured number and a vibe treated alike | Ledger them apart: fact vs. assumption. Ask "stopwatch, or a vibe?" |
| An inference presented as a fact | Give inferences their own column so a falsified premise cascades instead of silently poisoning a "fact." |
| Delivering low-regret "quick wins" mid-discovery | Solution leak. Record them, set aside, converge first. |
| Gathering until you run out of time | Converge on information gain, not the clock. |
