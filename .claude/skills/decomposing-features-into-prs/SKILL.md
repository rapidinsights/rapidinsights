---
name: decomposing-features-into-prs
description: Use when scoping a multi-step feature with production blast radius, sequencing risk, or cross-cutting refactor — before invoking writing-plans or any implementation tool — when the work is too large or risky to land as one PR
---

# Decomposing Features into PRs

## Overview

Upfront PR scoping is architectural work. It calibrates risk and review surface, decides what gets the careful eye of a reviewer, and shapes the order in which work meets reality. **The right slice ships value; the wrong slice ships dead code.** A good decomposition produces a small set of PRs that each land safely, deliver something on their own, and survive a future where the next PR never gets built.

Use this skill *before* invoking writing-plans (or Spec Kit `/specify`, or any other implementation tool) so the spec that goes in is already scoped to a single shippable unit.

Announce: "I'm using the decomposing-features-into-prs skill."

## When to Use

Triggers (any of these → use the skill):

- A feature spec spans multiple visible behaviors, multiple subsystems, or a migration plus code change
- Work touches production systems where rollback is expensive
- Sequencing matters (migration before code, contract before consumer, flag before gated path)
- An architectural commitment is being made (canonical model, schema choice, public API surface)
- A cross-cutting refactor touches many files for one reason
- Validation is needed mid-flight — you want PR 1 in production before committing to PR 2
- Multiple reviewers/stakeholders need different parts

**Don't use this skill (single PR is right) when:**

- Uniform/repetitive changes ("add field X to N entities") — splitting creates artificial seams
- Isolated greenfield with no production exposure and clear boundaries
- Throwaway/prototype work you'll iterate destructively on
- The split would create a worse intermediate state than not splitting

## Single PR or Multi-PR?

The default is **single PR**. Multi-PR is what you do when the work refuses to fit. Agents over-decompose far more often than they under-decompose, so the flowchart asks the "don't decompose" questions first.

```dot
digraph single_or_multi {
    rankdir=LR;
    spec [label="Feature spec\nin hand"];
    uniform [label="Uniform/repetitive\nchange?", shape=diamond];
    green [label="Isolated greenfield,\nno production exposure?", shape=diamond];
    throw [label="Throwaway/\nprototype?", shape=diamond];
    awkward [label="Would splits create\na worse intermediate\nstate than not splitting?", shape=diamond];
    one [label="Single PR\n(use single-PR variant\nof the template)", shape=box, style=filled, fillcolor="#e8f5e8"];
    many [label="Multi-PR\n(continue to step 5\nof the process)", shape=box, style=filled, fillcolor="#fff4e0"];

    spec -> uniform;
    uniform -> one [label="yes"];
    uniform -> green [label="no"];
    green -> one [label="yes"];
    green -> throw [label="no"];
    throw -> one [label="yes"];
    throw -> awkward [label="no"];
    awkward -> one [label="yes"];
    awkward -> many [label="no"];
}
```

## The Process

1. **Announce** the skill is in use.
2. **Read the spec.** Identify scope, contracts introduced, systems touched.
3. **Calibrate blast radius.** Use the heuristics in the [Calibration Heuristics](#calibration-heuristics) section. Output: high / medium / low + a one-line rationale.
4. **Decide single-PR vs multi-PR** via the flowchart above. If single PR, jump to step 8.
5. **List candidate vertical slices** — 3 to 7. Each slice is named for the user-visible or operationally-visible behavior it ships, not for the layer it touches.
6. **Apply the 4-question test (below) to each slice.** Discard or merge any slice that fails any question. Re-order so PR N never requires PR N+1 to be useful.
6a. **Map the dependency graph.** For each slice, write down which earlier slice it *hard-depends* on (cannot start without). Slices with no hard dependency on each other are parallel-development candidates — flag them; see [Parallel Development](#parallel-development) below for worktree handoff.
7. **Detail PR 1 and PR 2** using the per-PR spec contents below — *if both are definite next steps.* If only PR 1 is definite (PR 2 is conditional — e.g., "only if a second consumer arrives"), detail PR 1 and sketch PR 2 with its conditional trigger; don't fabricate speculative design. **Sketch PRs 3+ as one-line intentions only** (optionally with a rough size S/M/L for capacity planning — no acceptance criteria, no contracts, no per-PR spec). **Exception:** for high-blast-radius features, also detail the irreversible-cutover PR even if it's PR 3+ (e.g., the NOT-NULL constraint after a backfill; the legacy-API removal after dual-write). The reason for sketching is *the architecture you assumed will be wrong*; the reason for detailing is *this step is hard to take back*. When both apply, detailing wins.
8. **Write the decomposition doc** using the template at `decomposition-template.md`. Save to a project-appropriate location (typical: `docs/decompositions/<feature>.md`).
9. **Handoff:** point the user to PR 1's fenced "Spec" block. They paste it verbatim into their implementation tool (e.g., `/specify <paste>`). Do not paraphrase, summarize, or merge with conversation context on the way in. Do not paste the Reviewer notes.

## Vertical Over Horizontal

A good slice ships an observable behavior end-to-end (schema + API + UI + test + flag if needed), scoped to one thing a user or operator can see. A bad slice ships a layer (all schema, then all API, then all UI) — each layer is dead code until the last one lands.

**The trap:** horizontal slices LOOK reasonable. "PR 1: add the table. PR 2: add the API. PR 3: add the UI." Each is small, each is independently mergeable, each has clean review boundaries. But until PR 3 ships, nothing works. If priorities shift after PR 1, you've shipped dead code with a passing test suite.

**Example — OAuth login (Google) alongside existing email/password auth.**

Horizontal (bad), 5 PRs:

1. Provider interface + plugin registry — no concrete provider
2. Google provider implementing the interface — no caller
3. `oauth_providers` table + Google row — no reader
4. `/auth/oauth/:provider/callback` route + linking logic — no UI
5. "Sign in with Google" button + start route

Each PR is "mergeable." None deliver value alone. The interface designed in PR 1 was justified by what reviewers wanted to see in isolation, not by a real second consumer — so when GitHub OAuth lands two quarters later, the interface gets reshaped anyway.

Vertical (good), 1-2 PRs:

1. End-to-end "Sign in with Google" behind a flag: table + Google provider (no interface, just a concrete class) + callback + linking + button + flag. Smaller than the sum of the horizontal split because there's no abstraction layer for hypothetical future providers. Ships value the moment the flag flips.
2. *(Only if a second provider is actually being added)* Extract the provider interface when the second consumer exists.

**Exception:** when a horizontal layer is genuinely the unit of work — e.g., a pure schema expansion as part of an expand–migrate–contract sequence, where the next phase is days or weeks away — own that, call it out, and don't pretend it's vertical. But it's still ONE PR for the whole expansion phase, not a separate PR per table.

### What the vertical PR 1's Spec block looks like

PR 1's Spec block, pasted verbatim into `/specify` — WHAT/WHY in business language, zero HOW.

```text
Sign In With Google

## User Story (P1)

A user with a Google account can sign in to the app with one click via a "Sign in with Google" button alongside the existing email/password option. If the user already has an email/password account using the same Google email (verified by Google), the two identities link automatically and they sign in to the existing account. Otherwise a new account is created from the Google profile.

**Why this priority:** This is the entire feature's value. Anything less than end-to-end Google sign-in doesn't ship the feature.

**Independent Test:** A user clicks "Sign in with Google" on the login page, authenticates with Google, and is redirected back to the app signed in to a session indistinguishable from email/password sign-in. Verifiable end-to-end with one Google test account, no other PR required.

**Acceptance Scenarios:**
1. **Given** a user with no existing account, **When** they sign in with Google for the first time, **Then** a new account is created from their Google profile and they are signed in.
2. **Given** a user with an existing email/password account whose email matches a verified Google email, **When** they sign in with Google, **Then** the Google identity is linked to the existing account and they are signed in.
3. **Given** a user who previously linked Google, **When** they sign in with Google again, **Then** they are signed in to the same account.
4. **Given** the feature flag is off, **When** a user visits the login page, **Then** no "Sign in with Google" button is rendered and the Google callback path returns not-found.

## Edge Cases

- What happens when Google returns an unverified email? (Reject linking; do not silently bind to an existing account.)
- What happens if the user closes the Google consent screen mid-flow? (Return to the login page with no partial session created.)
- What happens if the Google identity is already linked to a different internal user? (Reject sign-in with a clear error; do not switch the linkage.)

## Functional Requirements

- **FR-001:** System MUST authenticate users via Google using standard OIDC with the user's email and profile.
- **FR-002:** Users MUST be able to link a Google identity to an existing email/password account when the Google-verified email matches.
- **FR-003:** System MUST create a new user account from the Google profile when no matching email exists.
- **FR-004:** System MUST NOT render the "Sign in with Google" UI or accept Google callbacks when the feature flag is off.
- **FR-005:** Sessions established via Google MUST be indistinguishable downstream from sessions established via email/password (same session shape, same expiry behavior, same downstream authorization).

## Key Entities

- **Identity provider configuration:** A configured external auth source (initially Google), with display name, enablement flag, and a reference to where credentials are stored.
- **External identity link:** Associates an external provider's verified user identifier with an internal user account. Unique per (provider, external user) pair.

## Success Criteria

- **SC-001:** A user can complete the Google sign-in flow end-to-end (click to landed) in under 10 seconds on the happy path.
- **SC-002:** Zero cross-account linkages during rollout — no user is ever signed in as a different user due to OAuth linking logic.
- **SC-003:** When the feature flag is off, zero Google-related UI is rendered and zero callback traffic is accepted (verifiable via metrics).

## Assumptions

- Google OAuth client credentials are provisioned out-of-band in the existing secret store before this PR ships.
- The existing session mechanism is reusable as-is.
- This PR is gated behind a server-side feature flag, off by default; flipping is a separate operational decision.

## Non-Goals

- Adding a second provider (e.g., GitHub) — see PR 2 sketch.
- Account unlinking, multi-identity management UI, or "log in with X" account-recovery flows.
- Changing the existing email/password flow.
- An admin UI for managing the identity-provider configuration (ops-managed for now).
```

Reviewer notes for this PR (not in the Spec block) would hold: the table schemas, the callback route pattern, JWKS verification details, dashboard URLs for the SC-### criteria, deploy ordering, rollback, and the 4-question check — all HOW.

## The 4-Question Slice Test

For each candidate slice, answer all four:

1. **Can this PR land without breaking anything?** Yes/no + why.
2. **Does it deliver USER-VISIBLE or OPERATIONAL value alone, even if the next PR never ships?** Yes/no + what value. *"Doesn't break anything" is not value. Tests passing on dead code is not value.* "Operational value" includes things only operators see — an admin tool that works, a dashboard that lights up, a new provisioning capability, a recoverable failure mode that wasn't recoverable before. It does NOT include "this code compiles" or "sets up PR 2." If your answer to Q2 feels strained or generous, the slice is wrong; merge it into the PR that ships the user-visible behavior.
3. **Can a reviewer hold the whole change in their head?** Yes/no + rough file/LoC count.
4. **Are the seams with adjacent PRs clean?** Yes/no + what stays untouched.

If any answer is no, the slice is wrong. Merge it into a neighbor or re-cut it. Question 2 is the one agents misread most often — re-read it before answering.

## Detail PR 1 and 2 — Sketch the Rest

Plan PR 1 and PR 2 fully. Sketch PR 3+ as **named intentions only** (title + one-line intent + optional rough size S/M/L for capacity planning). No Spec block. No acceptance criteria. No phase tables. No sprint allocation.

**Why:** detailed planning of PR 3+ assumes the architecture you assumed for PR 1 was right. You usually find out it wasn't while building PR 1. Detailed forward planning becomes sunk cost that biases you against course-correcting.

**Exception — irreversible cutover steps:** if a later PR is the genuinely irreversible step (PR 3 = setting NOT NULL after a backfill; PR 4 = removing the legacy API after dual-write), detail that PR too. The reason for sketching is *the architecture will be wrong*. The reason for detailing is *this step is hard to take back*. When both pressures apply to a later PR, detailing wins.

**Under pressure to plan further** (PM, reviewer, teammate): show sketched PR 3+ as named intentions, offer S/M/L sizing for capacity, explain that detail comes after PR 1 ships. Non-negotiable on the *detailed* part; rough sizing is fine. See the Failure Modes rows on "PM wants the whole plan" and "Detail PR 3 just enough" for the in-the-moment tells.

## Calibration Heuristics

| Context | Default slicing |
|---|---|
| High blast radius (live customer data, payments, auth, billing) | Bias small. Each PR revertable in isolation. Slice by capability, not by layer. Feature-flag risky behavior. |
| Architectural commitments (canonical model, public API, schema choices hard to reverse) | Each commitment gets its own PR with explicit acceptance criteria. Don't fold commitments into feature PRs. |
| Greenfield / early MVP / no production users | Wider scopes fine. Tighten as real users approach. |
| Internal tooling / one-offs | Single PR usually right. Don't manufacture ceremony. |
| Throwaway / spike / prototype | Single PR. Often no decomposition doc needed unless you're keeping the work. |

Project-specific overrides (which systems are "high blast radius" for *this* codebase, deploy cadence, compliance gates) belong in the project's `CLAUDE.md`, not here.

## Per-PR Spec Contents

The per-PR detail is split into two blocks. The **Spec block** is the paste-ready payload for Spec Kit's `/specify` (or any spec-driven tool); it describes WHAT and WHY in business language. The **Reviewer notes** hold operational HOW and slice-correctness checks for human review.

### In the Spec block (paste into `/specify`) — WHAT/WHY only

Aligned with Spec Kit's `spec-template.md` structure so `/specify` produces high-quality output on first pass:

- **Feature name** — 2-4 words, action-noun format, technical terms preserved
- **User Story (P1)** — plain-language description of the primary user journey, with:
  - **Why this priority** — the user value this PR delivers
  - **Independent Test** — the specific action that proves it works, even if no other PR ships
  - **Acceptance Scenarios** — in `Given <state>, When <action>, Then <outcome>` format
- **User Story (P2)** *(optional)* — only if this PR ships a secondary user-visible behavior; same structure as P1
- **Edge Cases** — boundary conditions, error scenarios, dependency-failure modes
- **Functional Requirements** — numbered `FR-001: System MUST...` statements in business terms. Mark unknowns inline: `[NEEDS CLARIFICATION: <question>]`
- **Key Entities** — what data represents, attributes, relationships — *no column types, no schema syntax*. This discipline is genuinely uncomfortable on transactional/data-heavy systems where the line between "what it represents" and "what its shape is" feels fuzzy. Hold the line anyway — types are HOW. The concrete schema lives in Reviewer notes; Key Entities is the conceptual model.
- **Success Criteria** — numbered `SC-001:` measurable, technology-agnostic outcomes (user-facing AND operational, e.g. "operations team sees delivery rate within X seconds")
- **Assumptions** — explicit defaults inferred for things not specified
- **Non-Goals** — what this PR explicitly does NOT do

### In the Reviewer notes (do NOT paste into `/specify`) — HOW only

Operational detail Spec Kit's `/specify` doesn't want, but reviewers need to validate the slice:

- **4-question check** — Q1 lands, Q2 delivers value alone, Q3 reviewable, Q4 clean seams
- **Data and interface contracts (implementation)** — schema diffs, API signatures, event payload shapes, exact syntax
- **Observability implementation** — dashboard URLs, metric names, alert configs, log queries (the HOW that satisfies the SC-### Success Criteria)
- **Deploy / migrate ordering** *(when sequencing matters)* — what must deploy first, what gates the next ramp step
- **Rollback plan** — feature flag? schema-compatible revert? trigger metric? owner?
- **Dependencies (PRs / external systems)** — what must already be merged
- **Test approach (implementation)** — unit/integration/E2E layers, fixtures

Sketched PRs get only: title + one-line intent + optional S/M/L sizing. No Spec block.

## The Decomposition Doc

The artifact this skill produces. Default location: `docs/decompositions/<feature-name>.md` (override per project convention). It captures:

- The decomposition decision (single PR or multi, with rationale)
- Cross-cutting contracts that span PRs (canonical reference for the human reviewing the doc) — includes application-layer contracts (schemas, API signatures, event payloads) AND infrastructure contracts (IAM permissions, network policies, secret refs, resource shapes, K8s manifests, Terraform module changes). For multi-PR features that touch production infrastructure, IaC commitments are equally load-bearing as API commitments — treat them with the same gravity.
- PR 1 and PR 2 full spec (or PR 1 only, plus any later irreversible-cutover PR detailed)
- PR 3+ as named intentions
- A PR tracking table (URLs, branches, status) — see "GitHub Process" section
- A re-evaluation section appended after each PR ships

**Important: the Spec block for each PR must be SELF-CONTAINED.** The implementation tool (`/specify`, writing-plans, etc.) reads only the Spec block — it cannot follow references to the Cross-Cutting Contracts section or elsewhere in the doc. If your Spec block says "see the Cross-Cutting Contracts section above," fix it: reproduce the relevant contract subset inline. The Cross-Cutting Contracts section exists for the human reviewer to see the contracts in one place; the per-PR Spec blocks are independent paste-payloads that happen to reproduce overlapping content. Tolerate the duplication.

**Each detailed PR section contains TWO blocks:**

1. A fenced "Spec (paste into `/specify`)" block — pure natural-language spec, ready to copy-paste into your implementation tool verbatim.
2. A "Reviewer notes" section — the 4-question check, rollback plan, etc. Lives in the doc for the human reviewing the decomposition; **does NOT go into the implementation tool's prompt.**

Use the template at `decomposition-template.md`. Both multi-PR and single-PR variants are in there.

## Handoff to Implementation

Once the decomposition doc is written:

1. Point the user to PR 1's fenced **Spec** block.
2. They copy it as-is and paste into their implementation tool: `/specify <paste>` (Spec Kit), or hand to writing-plans, or whatever planning tool they use.
3. **Strict zero-edit rule.** Do not paraphrase, summarize, reformat, or merge with conversation context on the way in. If something's missing or wrong, fix it in the decomposition doc and re-emit — never patch on the way to the tool.
4. **Do not paste the Reviewer notes block.** Those are for human review of the decomposition, not for the implementation tool's prompt.

The reason for strict zero-edit: rewriting the spec on the way into the tool degrades signal and creates two sources of truth. The decomposition doc IS the spec. The implementation tool reads the spec.

## GitHub Process

When ready to open PRs against the decomposition doc, see [`github-process.md`](github-process.md) for PR title and branch conventions, the PR description template (which quotes the Spec block verbatim), and the status lifecycle (`planned` → `in-review` → `merged` → `shipped`). Status updates flow back to the decomposition doc's PR Sequence Overview table and Re-evaluation Notes — only the `shipped` transition unblocks PR 2+ re-evaluation.

## Parallel Development

Process step 6a's dependency graph identifies PRs with no hard dependency on each other; the template's `Parallel Work Opportunities` table records the pairs. For mechanics, defer to `superpowers:using-git-worktrees`. **Anti-pattern:** never parallelize PR 1 with anything before PR 1 ships — PR 1 is the learning checkpoint; parallel work past it commits to assumptions you haven't validated.

## Failure Modes

Each row pairs an **Excuse** (what you might think) with a **Reality** that includes both a **Tell** (how to recognize you're rationalizing) and a **Counter** (what to do instead). Use the Tells in-flight — they're what catches the rationalization in the moment instead of after the decomposition has set.

Grouped by trigger type so you can jump to the relevant category under pressure.

### Social pressure (authority / process expectations push the wrong way)

| Excuse | Reality |
|---|---|
| "PM wants the whole plan up front" | **Tell:** you're about to write acceptance criteria for PR 5. **Counter:** show sketched PR 3+ as named intentions with rough S/M/L sizing for capacity, and explain that detailed planning of PR 3+ is sunk cost — it will change once PR 1 ships. Detailed-everything plans please the PM in the moment but cost more when they're discarded. |
| "PRs over N lines don't get proper review" → split to hit a line ceiling | **Tell:** you're slicing by line count instead of by behavior, and the seam between PR 1 and PR 2 cuts mid-feature. **Counter:** the shippable unit is the unit, not a line count. Reviewer fatigue is real, but the answer is structure/tests/walkthrough — not artificial cuts that produce vanity slices. If the real shippable unit is genuinely too large to review, ask "what structure or test framing would make this reviewable," not "where do I cut it." |
| "Phase the work into sprints up front so the PM can size it" | **Tell:** you've drawn a sprint-by-sprint allocation before deciding what's in PR 1. **Counter:** sprint allocation past PR 1-2 is sunk-cost planning. Estimate PR 1 + offer rough S/M/L for PRs 3+; re-estimate after PR 1 ships. |

### Technical seduction (designs that look clean but cost)

| Excuse | Reality |
|---|---|
| "This PR forces the abstraction question on its own" / "isolated review of the interface" | **Tell:** PR 1 is an interface/registry/plugin system, PR 2 is the first concrete consumer. **Counter:** speculative abstraction. The abstraction is justified by a real second consumer, not by review aesthetics. Ship the first consumer's full vertical slice; generalize only when the second consumer exists. |
| "Add the column / abstraction now to avoid a future migration" | **Tell:** you're adding a field, table, or interface that no consumer in the current PR uses. **Counter:** speculative design. YAGNI. Add it when the consumer exists. The migration "saved" is hypothetical; the dead code is real. |
| "Each PR is independently mergeable" (read as "doesn't break anything") | **Tell:** your Q2 answer for some PR is "doesn't break anything" or "tests pass." **Counter:** mergeable ≠ shippable. Q2 asks *delivers value alone*, not *fails to break things*. Dead code with passing tests is still dead code. |
| "Auth-critical / production-critical so small PRs are justified" | **Tell:** you're slicing aggressively because the topic feels scary, even though the changes are additive and behind a flag. **Counter:** true for irreversible changes (schema migrations, hard cutover, public API removal). NOT true for additive validation, new behavior behind a flag, or anything revertable. Calibrate by what's actually irreversible, not by topic. (Compliance gating like SOC2/PCI is a separate axis — if the change-management process gates every auth touch, slice to fit the gate's review window.) |

### Sequencing / timing errors (right idea, wrong place in time)

| Excuse | Reality |
|---|---|
| "Telemetry / observability / runbook deserves its own PR" | **Tell:** you have a PR titled "Add metrics for X" or "Dashboards for Y" with no behavior change. **Counter:** almost never. Observability ships with the behavior it observes. A dashboard PR alone is ceremony. Observability is a field of the Per-PR Spec for high-blast PRs — it rides with the feature. |
| "Rollout config flip is its own PR" | **Tell:** you have three PRs labeled "Enable for 10%," "Enable for 50%," "Enable for 100%." **Counter:** for additive low-blast changes, the flip ships with the code. For high-blast progressive rollouts, the ramp protocol is ONE PR (with gating criteria + rollback triggers), not one PR per ramp step. |
| "Splitting expand–migrate–contract into one PR per step is the safe way" | **Tell:** you have separate PRs for "add nullable column," "backfill," and "create index" on the same set of tables. **Counter:** one PR per *phase*, not per step. The whole expansion (add column nullable + index + backfill, all 20 tables) is ONE PR. The contraction (set NOT NULL) is the next PR. Splitting further is horizontal slicing dressed as safety. |
| "PR 1 is done, I'll start PR 2 now" (after merge but before deploy) | **Tell:** you're opening PR 2's branch the same day PR 1 merged, before deploy + soak. **Counter:** "shipped" means merged + deployed + observed without regression — not just merged. Starting PR 2 before PR 1 is observed loses the entire ship-and-re-evaluate cycle. |

### Spec hygiene (post-decomposition cheating)

| Excuse | Reality |
|---|---|
| "PR 2 'delivers value alone' but I had to stretch to find it" | **Tell:** your Q2 answer reads "well, an operator could technically..." or "it enables future..." **Counter:** if Q2 felt strained or generous, you're rationalizing. Genuine operational value means an operator gains a new capability, not "this enables PR 3." When the honest answer is "it sets up the next PR," merge it into the PR that ships the user-visible behavior. |
| "I'll lightly reformat the spec on the way into the implementation tool" | **Tell:** you're about to paste the Spec block into `/specify` with edits, paraphrasing, or context merges. **Counter:** no. Strict zero-edit. If it needs reformatting, fix it in the decomposition doc and re-emit. Two sources of truth = two sources of bugs. |

## Red Flags — STOP

If any of these are true, stop and re-decompose:

- The plan has 5+ PRs and you wrote detailed specs for all of them
- PR 1 adds infrastructure (interface, table, middleware) with no consumer in the same PR
- Two adjacent PRs would HAVE to land within the same week to be useful → they're one PR
- "Observability," "telemetry," "runbook," "rollout," or "hardening" appears as its own PR title
- The decomposition was driven by a line-count rule rather than a slice judgment
- A phase/sprint breakdown showed up before the slice judgment
- You're about to paste a spec into the implementation tool with edits "to clean it up"
- The single-PR doc you wrote runs >120 lines (the Spec Kit-aligned format costs ~60 lines minimum; anything well past that means you're recreating ceremony you ruled out)

All of these mean: re-cut the slices using the 4-question test, ruthlessly merge or drop scaffolding-only PRs, and trim detailed planning past PR 2.

## Quick Reference

**Decompose into multiple PRs when:** production blast radius, sequencing risk, architectural commitment, cross-cutting refactor, validation needed mid-flight, multiple reviewer constituencies.

**Single PR when:** uniform/repetitive changes, isolated greenfield, throwaway, splits create worse intermediate states.

**4-question test (every slice must pass all 4):**

1. Lands without breaking?
2. Delivers user-visible or operational value alone?
3. Reviewer holds it in head?
4. Clean seams?

**Detail PR 1 + PR 2. Sketch the rest as named intentions only.** No Spec block, no acceptance criteria, no phase tables, no sprint allocation for PR 3+.

**Per-PR Spec block includes:** goal, non-goals, acceptance criteria, contracts, dependencies, test approach. (Rollback goes in Reviewer notes.)

**Handoff:** paste PR 1's fenced Spec block into your implementation tool verbatim. Never the Reviewer notes. No edits.
