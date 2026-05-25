# Decomposition Doc Template

Two variants below. Pick the one matching your decision (multi-PR or single-PR) and copy it into the actual decomposition doc location (default: `docs/decompositions/<feature>.md`, or whatever the project uses). Replace `<angle bracket>` placeholders. Keep the fenced "Spec" blocks intact — those are what get pasted into the implementation tool verbatim.

The outer template is shown in `````markdown ... ````` (four-backtick) fences so the inner triple-backtick spec blocks render correctly. When copying into your doc, drop the outer four-backtick wrappers.

---

## Multi-PR Variant

````markdown
# <Feature Name> — PR Decomposition

> **Source spec:** `<path/to/source/spec.md>` (if persisted; otherwise the conversation that produced this doc)
> **Status:** [ ] PR 1 detailed  [ ] PR 2 detailed  [ ] PR 3+ sketched  [ ] PR 1 shipped

## Goal

<One sentence. What does the whole feature do?>

## Non-Goals (whole feature)

- <Explicit out-of-scope items for the entire feature>

## Calibration

- **Blast radius:** <high / medium / low — and why>
- **Sequencing constraints:** <what must ship before what, and why>
- **Architectural commitments:** <new contracts, canonical models, public APIs introduced>

## Cross-Cutting Contracts

<Interfaces, schemas, or data shapes that span PRs. Canonical reference for the human reviewer.

**Note:** each per-PR Spec block below must reproduce the relevant contract subset inline — the implementation tool reads only the Spec block and cannot follow references back to this section. Tolerate the duplication; the canonical version here is what reviewers compare against when checking consistency across PRs.

**Application-layer contracts:** event payload shapes, canonical model definitions, authorization scope names, API signatures.

**Infrastructure contracts** *(when the feature touches IaC):* IAM role/policy changes, network policies, secret refs (key names, not values), resource shapes (CPU/memory/replica), K8s manifests, Terraform module versions. Treat these with the same gravity as API contracts — a breaking IaC change is as load-bearing as a breaking API change.>

## PR Sequence Overview

| # | Title | Branch | PR URL | Status | Depends on (hard) | Lands alone? | Delivers value alone? | Notes |
|---|-------|--------|--------|--------|--------------------|--------------|------------------------|-------|
| 1 | <name> | `<feature>/pr-1-<slug>` | <github URL once opened> | planned | none | yes | yes | <one-line intent> |
| 2 | <name> | `<feature>/pr-2-<slug>` | — | planned | PR 1 / none | yes | yes | <one-line intent> |
| 3 | <name> | — | — | sketched | TBD | TBD | TBD | sketched only |
| 4 | <name> | — | — | sketched | TBD | TBD | TBD | sketched only |

> **Status lifecycle:** `planned` → `in-review` (PR opened) → `merged` → `shipped` (deployed + observed without regression). Only the `shipped` transition unblocks re-evaluation of subsequent PRs.

## Parallel Work Opportunities

<List pairs/triples of PRs with no hard dependency on each other. These are git-worktree candidates — see the `superpowers:using-git-worktrees` skill for setup. Omit this section if everything is strictly sequential.>

| Parallel set | File-overlap risk | Recommended? | Notes |
|---|---|---|---|
| PR 2 ‖ PR 3 | low / medium / high | yes / no | <e.g., "disjoint file sets, both branch from main; backend vs. independent frontend"> |

**Anti-pattern to avoid:** parallelizing PR 1 with anything before PR 1 ships. PR 1 is the learning checkpoint — parallel work past it gates against a stale picture.

---

## PR 1: <Title>

### Spec (paste into `/specify` — WHAT/WHY only, no implementation HOW)

```text
<Feature name — 2-4 words, action-noun format, technical terms preserved>

## User Story (P1)

<Plain-language description of the primary user journey this PR ships. Business terms only — no tech stack, no schema, no API names.>

**Why this priority:** <The user value this PR delivers and why it goes first.>

**Independent Test:** <One-sentence answer to "how do I verify this works end-to-end, with nothing else shipped?" This is the slice-correctness check (Q2 in disguise) — if you can't answer it crisply, the slice is wrong. Distinct from Acceptance Scenarios below, which is the full multi-case behavior spec.>

**Acceptance Scenarios:**
1. **Given** <initial state>, **When** <action>, **Then** <observable outcome>
2. **Given** <initial state>, **When** <action>, **Then** <observable outcome>

## User Story (P2) — optional, only if this PR ships a secondary user-visible behavior

<...same structure as P1...>

## Edge Cases

- What happens when <boundary condition>?
- How does the system handle <error scenario>?
- What happens if <dependency is unavailable / inconsistent / slow>?

## Functional Requirements

- **FR-001:** System MUST <specific capability in business terms>.
- **FR-002:** Users MUST be able to <key interaction>.
- **FR-003:** System MUST <data behavior, e.g., "preserve existing user sessions when feature flag is off">.

If anything is unspecified, mark inline: `[NEEDS CLARIFICATION: <question>]`.

## Key Entities

- **<Entity name>:** <What it represents, key attributes, relationships to other entities — no column types, no schema syntax. Implementation lives in Reviewer notes.>

## Success Criteria

- **SC-001:** <Measurable, technology-agnostic outcome — e.g., "Users can complete sign-in with Google in under 10 seconds">.
- **SC-002:** <Operational metric — e.g., "Cross-tenant query leakage rate stays at zero through the rollout">.

## Assumptions

- <Inferred default about users, scope, environment, or dependencies>.
- <e.g., "Wildcard DNS for tenant subdomains is already provisioned">.

## Non-Goals

- <Explicit out-of-scope for this PR>.
```

### Reviewer notes (do NOT paste into `/specify`)

- **4-question check:**
  - Lands on own without breaking? <yes — why>
  - Delivers user-visible or operational value if PR 2 never ships? <yes — what value>
  - Reviewer holds it in head? <yes — approx file count / LoC>
  - Clean seams with adjacent PRs? <yes — what stays untouched>
- **Data and interface contracts (implementation HOW):** <Schema diffs, API signatures, type changes, event payloads — exact syntax. Cross-reference the Cross-Cutting Contracts section if those shapes already canonicalized there.>
- **Observability implementation:** <Dashboard URLs, metric names, alert configs, log queries. The WHAT is in Success Criteria; this is the HOW that satisfies SC-###.>
- **Deploy / migrate ordering** *(when sequencing matters):* <Must deploy after migration X? Must NOT pass N% rollout until prior PR is at 100%? Any database expansion that must precede this PR's deploy?>
- **Rollback plan:** <Feature flag? Schema-compatible revert? Manual data fix? Pre-conditions to flip back? What metric/alert triggers the rollback? Who has the perm to flip it?>
- **Dependencies (PRs / external systems):** <Prior PRs that must be merged; existing systems relied on.>
- **Test approach (implementation):** <Unit / integration / E2E layers; what gets fixtured; manual verification steps. The WHAT is in Acceptance Scenarios; this is the HOW that verifies them.>

---

## PR 2: <Title>

### Spec (paste into `/specify`)

```text
<same Spec Kit-aligned structure as PR 1: User Story P1 (+ optional P2), Edge Cases, Functional Requirements, Key Entities, Success Criteria, Assumptions, Non-Goals>
```

### Reviewer notes (do NOT paste into `/specify`)

<same structure as PR 1: 4-question check, contracts, observability impl, deploy ordering, rollback, dependencies, test approach>

---

## PR 3+ (Sketched Only)

> Detailed planning is deferred until PR 1 ships and we learn what was wrong about the architecture we assumed. These are named intentions; expect them to change. Do NOT write Spec blocks for these yet — that planning is sunk cost.

- **PR 3 — <name>:** <one-line intent>
- **PR 4 — <name>:** <one-line intent>

## Re-evaluation Notes

> Append (don't edit) after each PR ships. **"Shipped" means merged + deployed + observed without regression** — NOT just merged. For continuous-deploy teams: after the post-deploy soak. For release-train teams: after the release cuts and goes out. Don't start re-evaluating PR 2+ off a merged-but-undeployed PR 1 — you'll plan against a stale picture.

### After PR 1 shipped (<YYYY-MM-DD merged + YYYY-MM-DD deployed/observed>)

- <What surprised us>
- <What changed about the plan for PR 2+>
- <Any sketched PRs that should now be detailed, re-cut, or dropped>
- <Observability gaps spotted during the soak — any new dashboards/alerts needed for PR 2>
````

---

## Single-PR Variant

````markdown
# <Feature Name> — PR Decomposition

> **Source spec:** `<path/to/source/spec.md>` (if persisted; otherwise the conversation that produced this doc)
> **Decision:** Single PR. Rationale below.

## Goal

<One sentence>

## Single-PR Rationale

<Which "don't decompose" trigger applied: uniform/repetitive changes, isolated greenfield, throwaway/prototype, or splits would create awkward intermediate states. Be specific — name the trigger and quote the relevant facts.>

## PR 1: <Title>

### Spec (paste into `/specify` — WHAT/WHY only, no implementation HOW)

```text
<Feature name — 2-4 words, action-noun format>

## User Story (P1)

<Plain-language description of the user journey. Business terms only.>

**Why this priority:** <The user value this PR delivers.>

**Independent Test:** <The specific action that proves it works end-to-end.>

**Acceptance Scenarios:**
1. **Given** <initial state>, **When** <action>, **Then** <observable outcome>
2. **Given** <initial state>, **When** <action>, **Then** <observable outcome>

## Edge Cases

- What happens when <boundary condition>?
- How does the system handle <error scenario>?

## Functional Requirements

- **FR-001:** System MUST <specific capability>.
- **FR-002:** Users MUST be able to <key interaction>.

## Key Entities

- **<Entity name>:** <What it represents, attributes, relationships — no schema syntax.>

## Success Criteria

- **SC-001:** <Measurable, technology-agnostic outcome.>

## Assumptions

- <Inferred default about scope, environment, or dependencies.>

## Non-Goals

- <Explicit out-of-scope.>
```

### Reviewer notes (do NOT paste into `/specify`)

- **Data and interface contracts (implementation HOW):** <Schema diffs, API signatures, type changes — exact syntax.>
- **Test approach (implementation):** <Unit / integration / manual; what fixtures.>
- **Rollback plan:** <Feature flag? Schema-compatible revert? Trigger? Owner?>
````

---

## Notes on use

- The **Spec block is the verbatim handoff payload** and is optimized for Spec Kit's `/specify`. It contains WHAT/WHY in business language, structured as User Stories (P1/P2 with Independent Test + Given/When/Then), Edge Cases, Functional Requirements (FR-###), Key Entities, Success Criteria (SC-###), Assumptions, Non-Goals. **NO implementation HOW** — no schema syntax, no API signatures, no library names, no tech stack.
- The **Reviewer notes hold all the HOW**: schema diffs, API signatures, observability dashboard URLs and metric names, deploy/migrate ordering, rollback specifics, test implementation. These are operational concerns Spec Kit's `/specify` doesn't want in the spec.
- The split between Spec and Reviewer notes is load-bearing. If you mix HOW into the Spec block, `/specify` will inherit your tech decisions and confuse its own planning. The Spec block must read like a product manager wrote it; the Reviewer notes like an engineer wrote it.
- If you find yourself wanting to write a full Spec block for PR 3+, **stop.** That's the sunk-cost planning failure mode. Sketch only. Exception: a high-blast irreversible-cutover PR (NOT NULL constraint, legacy-API removal after dual-write) deserves detail even when it's PR 3 or later.
- Sketched PRs can carry a rough size estimate (S/M/L) for PM capacity planning. They must NOT carry acceptance criteria, contracts, or Spec blocks.
- If you find yourself wanting a single-PR doc that runs past ~60 lines (Spec block can be ~40 of those), **stop.** You're recreating ceremony you ruled out by choosing single-PR.
