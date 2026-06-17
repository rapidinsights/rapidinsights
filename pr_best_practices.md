# The Perfect Pull Request: Best Practices and a Copy-Paste Template for Small Teams

## TL;DR
- **Keep PRs small and single-purpose (target ~200–400 changed lines), write a description that explains the "why," and adopt a short, decisive PR template** — these three habits capture most of the value of code review and are backed by the largest empirical studies (SmartBear/Cisco, Google, Microsoft Research).
- For a **1–3 person team, lightweight discipline beats heavyweight process**: enforce PRs via branch protection, do rigorous self-review when no second reviewer exists, automate everything checkable (CI, linting, dbt tests, `terraform plan`), and keep the human checklist to items a machine cannot verify.
- For your **data/analytics consultancy**, extend the generic template with a small set of data-specific prompts — schema changes, backfills, idempotency, dev-vs-prod data validation, and rollback/full-refresh notes — because "data review" is a distinct risk surface beyond code review.

## Key Findings

### Pull request size is the single highest-leverage variable
The most-cited evidence is the SmartBear study of a Cisco Systems team (2,500 reviews, ~3.2 million lines of code over 10 months). It found that defect-detection effectiveness is highest when a reviewer examines **200–400 lines of code at a time**, spread over **no more than 60–90 minutes**. As SmartBear's *11 Best Practices for Peer Code Review* puts it: "with the review spread over no more than 60-90 minutes, you should get a 70-90% yield; in other words, if 10 defects existed, you'd find 7-9 of them." Beyond ~400 LOC, the ability to find defects drops sharply; review pace faster than ~500 LOC/hour also collapses effectiveness. Google's engineering practices echo this: small changelists ("CLs") get reviewed faster, more thoroughly, are less likely to introduce bugs, and are easier to merge and roll back. LinearB's engineering benchmarks (drawn from millions of PRs across thousands of teams) put a concrete number on the "elite" target: their elite-team cohort averages **under ~219 lines changed per PR**.

Microsoft Research adds a complementary finding about *files*: in Bosu, Greiler & Bird's study at Microsoft, "the more files that are in a change, the lower the proportion of comments in the code review that will be of value to the author." So both line count and file count degrade review quality — the practical rule is **one logical change per PR**.

### Code review is about more than catching bugs
The landmark study here is Bacchelli & Bird, "Expectations, Outcomes, and Challenges of Modern Code Review" (Microsoft Research, ICSE 2013), based on observing 17 developers, surveying over 1,000 engineers and managers, and hand-coding 570 review comments from Microsoft's CodeFlow tool. Its headline: finding defects is the top *motivation* but not the main *outcome* — "reviews are less about defects than expected and instead provide additional benefits such as knowledge transfer, increased team awareness, and creation of alternative solutions to problems." In their sample, defect-related comments were only the fourth most common category (14%), while "code improvements" were the most common (29%). This matters enormously for a small team: even when bugs are rare, review spreads knowledge across a tiny team (reducing bus-factor risk) and forces clearer thinking. Google's case study (Sadowski et al., ICSE 2018) reached similar conclusions, framing review around education, maintaining norms, gatekeeping, and accident prevention.

### Titles and descriptions: explain what, why, and how
Authoritative guidance (Google's "Writing good CL descriptions," The Pragmatic Engineer, Atlassian, HackerOne/PullRequest) converges on:
- **Title**: short, imperative, specific. "Fix race condition in session cleanup that caused 502s under load" beats "Fix bug." A verb-first present-tense convention ("Add…", "Fix…", "Refactor…") keeps history scannable.
- **Description**: explain the **why** (business/engineering goal), the **what** (high-level summary of the change), and the **how** when non-obvious. Commit messages describe what/why at the code level; the PR description should synthesize them into a narrative, not just paste the commit log.
- **Link the issue/ticket** ("Fixes #123" auto-closes on merge) so context is one click away.
- **Screenshots / before-after** for any UI or, for data work, query-diff and dashboard screenshots.

### Self-review before requesting review
The SmartBear study found that authors who annotate and prepare their review for others produce far fewer defects — the act of self-review surfaces bugs. This is the highest-value habit for near-solo teams: read your own diff with fresh eyes, in a different tool/view if helpful, before assigning a reviewer.

### Commits, and squash vs. merge vs. rebase
- **Conventional Commits** (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, etc., with `BREAKING CHANGE:`/`!`) make history machine-readable, drive automated changelogs and semantic-version bumps (`fix`→patch, `feat`→minor, breaking→major), and instantly communicate intent. Low cost, compounding benefit; enforceable with commitlint + Husky.
- **Merge strategy** is contextual, but for small teams the dominant recommendation is **squash-and-merge for feature/fix branches**: one clean commit per PR keeps `main` history readable and rollbacks trivial, at the cost of losing intra-branch commit granularity. Keep merge commits for long-lived branches (e.g., release). Rebase keeps linear history but is riskier on shared branches.

### Draft PRs
GitHub draft PRs (available in all repositories, including free private repos as of May 2025) cleanly signal "work in progress": they cannot be merged and suppress CODEOWNERS review requests until marked ready. Use them to get early feedback, run CI, or share direction before the code is review-ready — replacing the old "WIP" title hack.

### Review process: turnaround, who reviews, approvals
- **Speed matters for team velocity.** Google's guidance is to respond to review requests fast (within one business day, ideally sooner). Per Sadowski et al. (2018), at Google "the overall (all code sizes) median latency for the entire review process is under 4 hours," and developers "wait for initial feedback on their change a median time of under an hour for small changes and about 5 hours for very large changes" — itself a powerful argument for small PRs. Benchmarks elsewhere are far slower: Rigby & Bird's medians (cited in Sadowski 2018) include 17.5h at AMD, 15.7h for Chrome OS, and 14.7–19.8h across three Microsoft projects, "and another study found the median time to approval at Microsoft to be 24 hours." A LinearB study of ~1,000,000 PRs found "pull requests are waiting on average 4+ days before being picked up," making review pickup "the no. 1 bottleneck in cycle time." A reasonable small-team target is first review (or self-review sign-off) within one business day.
- **Who/how many.** Google typically requires **one reviewer**; as Dr. Michaela Greiler summarizes Sadowski et al., "more than 75% of the code reviews have just one reviewer… Requiring only one reviewer seems like a conscious decision at Google and trades review rigor for speed." One competent reviewer captures most of the value; reserve two reviewers for business-critical code (auth, billing, data that feeds financials).
- **Tone.** Use comment-weight labels — "Nit:", "Optional:", "Consider:", "FYI:" — so non-blocking polish isn't read as mandatory. Comment on the code, not the person. Seek "continuous improvement," not perfection.

### How GitHub PR templates work (mechanics)
- A single default template lives at `.github/pull_request_template.md` (also valid in repo root or `docs/`). GitHub auto-populates the PR body from it.
- **Multiple templates** go in a `.github/PULL_REQUEST_TEMPLATE/` directory; authors select one via the `?template=name.md` query parameter (e.g., `?template=data_change.md&expand=1`). Note GitHub does *not* show a native dropdown picker for PR templates the way it does for issues — you trigger alternates via URL/links (e.g., buttons in your README).
- Templates must be on the **default branch** to take effect. You can set org-wide defaults via a `.github` community-health repo.
- Use GitHub-flavored markdown task lists (`- [ ]`) for checklists and `<details>` for collapsible sections to keep the form compact.

### What sections a high-quality template includes
Synthesizing GitHub docs, dbt Labs, Atlassian, the widely-copied Embedded Artistry template, and others, the durable sections are: **Summary/description (what & why)**, **Type of change** (feature/fix/refactor/docs/infra/breaking), **Related issue link**, **How tested / testing instructions**, **Screenshots or evidence**, **Breaking changes**, **Deployment/rollback notes**, and a short **author checklist**. Risk/rollback and breaking-change callouts are increasingly standard for anything touching production.

### Common pitfalls
- **Too long/bureaucratic → ignored.** Overly complex templates get skipped; checklist items that are vague ("code follows best practices") get rubber-stamped. There is real "checklist fatigue" literature in safety-critical fields. Every checkbox should be verifiable in under ~30 seconds and cover something CI cannot.
- **Generic prompts without context.** "Run tests" is weak; "run `dbt build` and paste results" is actionable.
- **Separate author chores from reviewer chores.** Keep the author checklist focused on what they must confirm before requesting review.
- **Stale templates.** Audit quarterly; retire unused fields.

### Tailoring for very small teams (1–3 devs) and solo work
- **PRs are still worth it solo.** Practitioners (Jonathan Hall, Will Kahn-Greene, Matt Robertson) and Jeff Atwood all argue self-review/PRs catch the silly mistakes you'd otherwise commit, create a documentation trail for your future self, and preserve good habits. Branch protection that *requires* a PR (even self-merged) prevents accidental direct pushes.
- **What's overkill:** mandatory multi-reviewer approval, heavyweight formal inspections, long mandatory checklists, required two-LGTM on every change.
- **What to keep:** small PRs, good descriptions, CI/automation as the "second reviewer," conventional commits, branch protection, and a *lean* template.
- **Async review:** for distributed/consultancy work, add estimated review time and self-comments to PRs so a part-time reviewer can pick up quickly; batch reviews into 1–2 daily windows rather than interrupt-driven (Google notes that interrupting your own focused coding to review is more expensive to the team than making the author wait briefly).

### Data-engineering-specific considerations (dbt / ETL / IaC / SQL)
"Data review" is a distinct discipline layered on top of code review. dbt Labs publishes the most-cited analytics PR template; its sections are Description & motivation, To-do before merge, Screenshots (DAG/lineage), Validation of models, Changes to existing models, and a Checklist ("My PR represents one logical piece of work," "commits look clean," "SQL follows style guide," "tests + docs added," "models materialized appropriately," "README updated"). The "In the Pipeline"/Recce extensions add explicit **dev-vs-prod data validation** (query diff, value diff, profile diff, schema diff) as proof of correctness.

For SQL/migrations and IaC, the literature converges on a small set of high-risk checks:
- **Schema changes**: list each changed model and the type of change; use the **expand/contract pattern** (add new, backfill, switch reads, then drop old) to stay backward-compatible and avoid downtime; set lock/statement timeouts.
- **Backfills**: must be **idempotent** and batched/throttled, with checkpoints and a pause mechanism; watch replication lag/CPU.
- **Migrations**: ship DDL and data backfills separately; always author a **rollback path** when you write the forward path; test up/down/up in CI to prove idempotency.
- **IaC (Terraform)**: the single most important review practice is requiring **`terraform plan` output in the PR**; reviewers check for unexpected destroys/force-replacements, security (no `0.0.0.0/0`, least-privilege IAM, encryption, no plaintext secrets), cost, and rollback-ability. Automate `fmt`/`validate`/`tflint`/`tfsec`/`checkov` in CI.

## Details: the recommended templates

### A) The lean default template (recommended for most small-team repos)
Place at `.github/pull_request_template.md`:

```markdown
## What & why
<!-- What does this change do, and why? Link the issue. -->
Fixes #

## Type of change
- [ ] Feature
- [ ] Fix
- [ ] Refactor / chore
- [ ] Docs
- [ ] Breaking change

## How I tested it
<!-- Commands run, what you verified, screenshots/evidence if relevant. -->

## Checklist
- [ ] One logical change; PR is reasonably small
- [ ] I self-reviewed the diff
- [ ] Tests/CI pass locally
- [ ] Docs/README updated if needed
- [ ] Breaking changes + rollback noted above (if any)
```

**Why each section:** *What & why* + issue link is the non-negotiable context reviewers (and future-you) need. *Type of change* takes 2 seconds and tells the reviewer what kind of review is needed (and drives changelogs if you use conventional commits). *How I tested* is the most underused, highest-value section. The *checklist* is deliberately five items, each verifiable in seconds and each covering something CI can't fully guarantee. **To go even leaner:** drop the checklist to just "self-reviewed" + "tests pass," or collapse Type of change.

### B) The data-change template (for dbt/SQL/pipeline/IaC repos)
Place at `.github/PULL_REQUEST_TEMPLATE/data_change.md` and link it from your README with `?template=data_change.md&expand=1`:

```markdown
## Description & motivation
<!-- What changed and why. Link issue/ticket. -->
Fixes #

## Type of change
- [ ] New model / table / pipeline
- [ ] Change to existing model (see "Changes to existing models")
- [ ] Schema change (DDL)  - [ ] Backfill / data migration
- [ ] Infra / IaC change
- [ ] Breaking change

## Lineage / DAG
<!-- Screenshot of the relevant DAG section, or terraform plan output for IaC. -->

## Validation (proof of correctness)
<!-- dbt build/test results; ad-hoc queries; dev-vs-prod query/value/profile/schema diff. -->

## Changes to existing models / migration notes
<!-- Full-refresh needed? Drop old models? Expand/contract stage? Run order? -->

## Data safety checklist
- [ ] One logical change; self-reviewed
- [ ] `dbt build` + tests pass (or CI green); new models have unique/not-null tests + docs
- [ ] Schema changes listed; backward-compatible (expand/contract) where needed
- [ ] Backfills are idempotent, batched, and safe to re-run
- [ ] Rollback / revert plan documented (incl. full-refresh or drop steps)
- [ ] No secrets/PII exposed; least-privilege; IaC plan reviewed
```

**What to trim if it's too heavy:** for internal/low-stakes repos, keep only Description, Validation, Changes-to-existing-models, and a 3-item checklist (self-reviewed / tests pass / rollback noted). Add the security and IaC lines only in repos that actually contain infrastructure or sensitive data.

## Recommendations (staged)

**Stage 1 — this week (near-zero cost):**
1. Add the lean default template to every active repo.
2. Turn on branch protection on `main`: require a PR before merge and require CI status checks to pass. (Solo-friendly: you can still self-merge, but no accidental direct pushes.)
3. Adopt squash-and-merge as the default and enable "auto-delete branch after merge."

**Stage 2 — this month:**
4. Adopt Conventional Commits; optionally enforce with commitlint + a CI check.
5. Stand up CI that runs your real checks (tests, linting; for data repos: `dbt build`/tests on modified models, `terraform plan` posted as a PR comment).
6. Set a team norm: first review (or self-review sign-off) within one business day; keep PRs reviewable in <60 minutes.

**Stage 3 — for the consultancy's data repos:**
7. Add the data-change template and wire dev-vs-prod data validation (e.g., query/profile/schema diffs) into the PR evidence section.
8. For any DB migration, require the expand/contract + rollback discipline and a `db-migration` label as a deliberate sign-off.

**Benchmarks that should change your process:**
- If most PRs exceed ~400 lines or touch many files → split work smaller; reviews are losing effectiveness (and you're well past LinearB's ~219-line elite average).
- If PR pickup/first-review regularly exceeds ~1 business day → assign a specific reviewer at creation, or add a stale-PR reminder.
- If checklist items are routinely checked without being done → the checklist is too long or too vague; cut it.
- If you're solo and never catching anything in self-review → still keep it; its value is also the documentation trail and habit preservation, not just defect-finding.

## Caveats
- **Line-count thresholds are guidelines, not laws.** The 200–400 LOC figures come largely from the single SmartBear/Cisco study and its many re-citations; "smallness" is really about one cohesive logical change, not a hard line count. Generated code, lockfiles, and mass renames legitimately inflate diffs.
- **Some widely-quoted stats are weakly sourced.** A circulating "reviewed code has 20–30% fewer defects (Microsoft)" claim could not be traced to a verifiable Microsoft primary source and should not be cited as such; the better peer-reviewed evidence that under-reviewed code ships more post-release defects is McIntosh, Kamei, Adams & Hassan (MSR 2014 / EMSE 2016), which found low review coverage and participation produce components with "up to two and five additional post-release defects respectively." Similarly, the often-quoted "code review catches 55–60% of defects" figures trace to Fagan's IBM inspection work and McConnell's *Code Complete*, not to a Microsoft production study — attribute carefully.
- **Vendor blogs have incentives.** Much PR-tooling content (Graphite, Axolo, LinearB, Codacy, etc.) is sound but markets a product; the underlying research (Google eng-practices, Bacchelli & Bird, SmartBear/Cisco, Sadowski et al., dbt Labs docs) is the more reliable backbone.
- **Context wins.** A fintech/regulated client warrants heavier review and auditability than an internal analytics repo; tune rigor to risk.
