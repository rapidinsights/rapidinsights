# GitHub Process for Decomposed Features

When the decomposition doc is written and you're ready to open PRs against it, this file is the convention layer. Conventions are defaults — override per project (typically via the project's `CLAUDE.md`), but adopt *something* equivalent. Without a process, the link between the decomposition doc and the actual PRs decays after PR 1 ships.

## PR title convention

`[<feature-slug>] PR N/M: <title>`

- Example: `[oauth-google] PR 1/2: End-to-end Sign in with Google behind flag`
- `M` reflects the *current* plan (sketched PRs count). When the plan changes after re-evaluation, the denominators update.

## Branch naming

`<feature-slug>/pr-N-<slug>`

- Example: `oauth-google/pr-1-end-to-end-google-flag`
- Branches group visually in `git branch --list` and in GitHub's branch picker.

## PR description template

Every PR opened against a decomposition doc must include:

```markdown
**Decomposition:** [<feature-slug>](../docs/decompositions/<feature-slug>.md) — PR N/M

**Spec for this PR:** [link to PR N section in the decomposition doc, anchored]

<the fenced Spec block, copy-pasted from the decomposition doc — same content the implementation tool received>

**Reviewer notes:** see [decomposition doc → PR N → Reviewer notes](anchor) for the 4-question check, rollback plan, deploy ordering, and observability implementation.
```

The description quotes the Spec block verbatim so reviewers don't need to context-switch. The link to Reviewer notes covers everything that didn't paste-verbatim.

**Strict rule:** the Spec block in the PR description must be identical to the Spec block in the decomposition doc. If you find yourself "lightly editing" it for the PR, fix it in the decomposition doc instead and re-emit.

## Status tracking in the decomposition doc

The `## PR Sequence Overview` table in the decomposition doc gains a `Status` column with this lifecycle:

| Status | Trigger |
|---|---|
| `planned` | PR exists in the doc but no branch / PR opened |
| `in-review` | PR opened in GitHub (link added to the table's `PR URL` column) |
| `merged` | PR merged to main |
| `shipped` | merged + deployed + observed without regression |

The `shipped` transition — not the `merged` transition — is what unblocks re-evaluation of PR 2+. Starting PR 2 after `merged` but before `shipped` is the failure mode called out in the main skill's Failure Modes table.

For continuous-deploy teams, `shipped` = after the post-deploy soak (typically a few hours to a day, depending on traffic). For release-train teams, `shipped` = after the release cuts and goes out.

## Doc update flow

After each status change:

1. Edit the `PR Sequence Overview` row (status, PR URL, branch).
2. On the `shipped` transition, append to the `## Re-evaluation Notes` section:
   - What surprised us
   - What changed about the plan for PR 2+
   - Any sketched PRs that should now be detailed, re-cut, or dropped
   - Observability gaps spotted during the soak that PR 2 should address

The re-evaluation step is non-negotiable for multi-PR features. Without it, the doc drifts and PR 2 plans against a stale picture of PR 1.
