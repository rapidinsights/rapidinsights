# Problem Discovery Framework — Full Method

A system of thinking for the **knowledge and discovery phase** — the work you do
*before* attempting a solution. Its goal is twofold: to gather all relevant
context, and to understand **which context is most important**. The framework is
designed to converge (know when to stop) rather than sprawl (gather forever).

---

## Core Principle: hold the problem space open

Most bad designs come from collapsing the problem space and the solution space
too early — you latch onto a solution, then retrofit the problem to justify it.
The single job of this framework is to **keep you in the problem space longer
than feels comfortable**, while still giving you a principled signal for when you
genuinely understand enough to move on.

Two spaces, kept strictly separate:

- **Problem space** — what is true, what is wrong, why, and what "solved" means.
- **Solution space** — how to fix it. *Off-limits during discovery.*

---

## The Spine

```
frame → decompose → ledger → hypotheses → evidence (ranked) → stress → convergence check
                        ▲                                    │
                        └──────────── loop ──────────────────┘
```

These are not strictly sequential. Once the hypothesis engine starts, the middle
of the framework is a loop: hypotheses drive evidence-gathering, evidence updates
the ledger, the updated ledger reshapes the hypotheses, and you repeat until the
convergence check passes.

---

## 1. Frame

Write the problem as a **statement, not a task**. Make these explicit before
anything else:

- **Who** has this problem, and **what does "solved" look like?** (success
  criteria / definition of done)
- The **observable symptom** vs. the **suspected cause** — keep them in separate
  columns.
- **In scope** vs. **deliberately out of scope.**

> Guiding move ("Are Your Lights On?", Gause & Weinberg): make sure you are
> solving the *right* problem before solving the problem *right*.

## 2. Decompose

Break the problem into an **issue tree** that is **MECE** — Mutually Exclusive,
Collectively Exhaustive. This produces a *map of where the unknowns live*. Its
value is coverage: it stops you from over-investigating one corner while a whole
branch goes unexamined. Coverage gaps become visible as empty branches.

**Pick the decomposition axis deliberately — don't brainstorm a flat list.** The
split logic, in descending order of rigour:

| Logic | How it splits | When |
|---|---|---|
| **Algebraic** | An identity: `total = A + B + C` (e.g. `Revenue = Price × Volume`, `Lead time = queue + process + transport`) | Whenever an equation exists — it is *provably* MECE, so prefer it |
| **Process** | Sequential stages: request → triage → build → review → ship | Funnels, pipelines, workflows |
| **Dichotomy** | Two opposing halves: new vs. existing customers, internal vs. external | Fast, clean, hard to make non-MECE |
| **Borrowed framework** | Someone else's structure (5 Forces, 4Ps) | Weakest — you are trusting a decomposition you didn't test |

Prefer algebraic when you can write the identity. If you cannot state the split
logic in one sentence, the branch isn't clean yet. Keep diagnostic branches
("why / where is X true") strictly separate from solution branches ("how to fix")
— the latter is the solution space leaking in (see §4).

## 3. The Ledger — facts / inferences / assumptions / unknowns

The central artifact you maintain throughout. Every claim about the problem lands
in exactly one column:

| Column | Meaning |
|---|---|
| **Facts** | Verified, evidence-backed observations. |
| **Inferences** | Derived from facts by logic — *not themselves observed.* True only as far as the facts and the reasoning hold. |
| **Assumptions** | Beliefs you are carrying but have *not* verified, and which no fact yet entails. |
| **Unknowns** | Open questions you know you cannot yet answer. |

The most common discovery failure is **assumptions wearing the costume of
facts.** The second most common is the **inference wearing the costume of a
fact** — a conclusion you *reasoned to* presented as something you *saw*. Keeping
inferences in their own column forces the question "which facts, and is the logic
sound?" and means a falsified premise cascades correctly instead of silently
poisoning a "fact." The ledger exists to keep these boundaries honest. Use
Socratic questioning and Five Whys to drill each branch of the tree toward root
cause and to surface the assumptions hiding underneath.

**Not all facts are equal — weight them.** A fact's strength is the quality of
its source, so annotate each:

- **Source quality** — a logged metric or system query outranks a stakeholder's
  recollection, which outranks a guess offered as fact.
- **Triangulation** — hold a load-bearing fact to ≥2 independent sources. Flag
  any single-source fact explicitly; it is the one most likely to be wrong.
- **Base rates over narratives** — "3 of the last 5 migrations slipped" beats
  "this feels risky."

A weakly-sourced "fact" that a hypothesis hangs on is itself a high-information
target for §5 — verifying it may be the cheapest way to move your confidence.

## 4. Hypotheses — the engine

This is the layer that makes discovery *converge* instead of sprawl, and it is
the mechanism that answers "which context is most important."

A hypothesis is **an assumption you have committed to testing.** Promoting an
assumption from the ledger into a hypothesis is the act that makes it earn its
place.

**Rules — the layer only works if you obey these:**

1. **Always carry ≥2 competing hypotheses** (Platt's *strong inference*). The job
   is not to confirm one; it is to *discriminate between rivals*. This is what
   structurally defeats confirmation bias — every piece of evidence is scored on
   which hypothesis it *kills*, not which it supports.
2. **Each hypothesis must be falsifiable** and paired with the question: *"What
   evidence would kill this?"* If you cannot answer that, it is not a hypothesis —
   it is a belief, so send it back to the assumptions column.
3. **Diagnostic hypotheses only — no solution hypotheses.** A hypothesis about the
   *nature or cause* of the problem belongs here ("the latency is lock
   contention, not I/O"). A hypothesis about the *fix* ("add an index") does not —
   that is the solution space leaking in. This boundary is what lets you have a
   hypothesis layer *without* prematurely solving.
4. **Hypotheses are provisional and confidence-tracked.** They are expected to
   die. Track a rough confidence on each and update it as evidence arrives.

## 5. Evidence-gathering, ranked by information gain

Hypotheses turn "what should I look at next?" from taste into arithmetic. A piece
of context is **important in exact proportion to how much it discriminates
between your live hypotheses.** Gather the highest-information-gain evidence
first — the observation most likely to *change your mind* — and convert resolved
unknowns into facts in the ledger.

This is the direct answer to the framework's goal of understanding *what context
is most important*: importance = discriminating power between competing
hypotheses, not raw relevance.

## 6. Stress the framing — inversion & pre-mortem

Before trusting your understanding, attack it:

- **Inversion:** "What would make this the *wrong problem* to solve?"
- **Pre-mortem:** "If our eventual solution fails spectacularly, why?"

This catches framing errors while they are still cheap — before any design effort
is sunk.

## 7. Convergence check — when to stop

Discovery is done **not when you have collected everything, but when remaining
hypotheses are sufficiently discriminated that more context would not change your
understanding of the problem.** When additional evidence stops moving your
confidence levels, you have enough context. Exit to design.

## 8. Hand off — communicate the converged problem

The framework's job ends at the problem/design boundary, but the *understanding*
still has to cross it intact. The handoff (the decision brief) communicates the
**problem**, not a solution — so this stage stays in the problem space. Structure
it top-down so a reader gets the answer before the derivation:

- **Lead with the converged problem statement** (the governing thought), then the
  load-bearing evidence beneath it — *Pyramid Principle*: think bottom-up during
  discovery, present top-down at handoff.
- **Frame the why with SCQA** — Situation (shared context) → Complication (what
  changed / what's wrong, quantified) → Question (the problem, answerable) →
  Answer (the converged problem statement). The Complication is where the
  ledger's strongest facts earn their place.
- **Every finding carries its "so what"** — an observation with no implication
  doesn't belong in the brief.
- **Carry the live ledger forward**: surviving assumptions, open unknowns, and
  the kill conditions that would reopen discovery travel with the handoff so
  design inherits the epistemic state, not just the conclusion.

This is a *communication* layer, not a solution layer — it reports what the
problem is and how confident you are, and stops there.

---

## The two paired layers, in one line each

- **Assumption** — a passive belief you are carrying.
- **Hypothesis** — an assumption you have committed to testing.

The ledger holds the former; the engine runs on the latter. Promotion between
them is the heartbeat of the framework.

---

## A fork to decide up front

Are your hypotheses about **root cause specifically** (a debugging-flavored
framework) or about **broader explanatory models of the problem** (a
design/strategy-flavored framework)? That choice changes what "evidence" and
"convergence" mean in practice — decide it when you instantiate the framework on
a real problem.

---

## Quick reference

1. **Frame** — problem statement, success criteria, scope, symptom vs. cause.
2. **Decompose** — MECE issue tree; pick the split axis deliberately (algebraic > process > dichotomy > borrowed); map where unknowns live.
3. **Ledger** — sort every claim into facts / inferences / assumptions / unknowns; weight facts by source quality and triangulation.
4. **Hypotheses** — ≥2 competing, falsifiable, diagnostic-only, confidence-tracked.
5. **Evidence** — gather by information gain (what discriminates the rivals).
6. **Stress** — inversion + pre-mortem on the framing.
7. **Converge** — stop when more context wouldn't change your understanding.
8. **Hand off** — communicate the converged problem top-down (Pyramid + SCQA), carrying the live ledger and kill conditions forward.
