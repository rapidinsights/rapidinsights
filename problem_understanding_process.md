# Problem Discovery Framework

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

## 3. The Ledger — facts / assumptions / unknowns

The central artifact you maintain throughout. Every claim about the problem lands
in exactly one column:

| Column | Meaning |
|---|---|
| **Facts** | Verified, evidence-backed. |
| **Assumptions** | Beliefs you are carrying but have *not* verified. |
| **Unknowns** | Open questions you know you cannot yet answer. |

The most common discovery failure is **assumptions wearing the costume of
facts.** The ledger exists to keep that boundary honest. Use Socratic questioning
and Five Whys to drill each branch of the tree toward root cause and to surface
the assumptions hiding underneath.

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
2. **Decompose** — MECE issue tree; map where unknowns live.
3. **Ledger** — sort every claim into facts / assumptions / unknowns.
4. **Hypotheses** — ≥2 competing, falsifiable, diagnostic-only, confidence-tracked.
5. **Evidence** — gather by information gain (what discriminates the rivals).
6. **Stress** — inversion + pre-mortem on the framing.
7. **Converge** — stop when more context wouldn't change your understanding.
