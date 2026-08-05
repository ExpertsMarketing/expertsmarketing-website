# Experts Marketing Website — Operating Model

Status: ACTIVE. Last verified: 2026-08-05.

This is the canonical description of how a change moves from "idea" to
"live on www.expertsmarketing.com." It supersedes the review-workflow
assumptions in `VERCEL_SETUP_PLAN.md` and `GITHUB_SETUP_PLAN.md`, which
predate this decision.

## 1. Identity model

The project is operated by a single GitHub identity:

| Role | Account | Notes |
|---|---|---|
| GitHub org owner / sole active contributor | `adeperthuis` (Arthur, adeperthuis@gmail.com) | Authors every commit and PR |
| Vercel team/project owner | adeperthuis@gmail.com | Same person, same account |
| Additional collaborator | Christian | Invited to Vercel for migration/support; not currently an active GitHub contributor |

This matters because it is the reason the workflow below looks the way
it does: GitHub will not let an account approve its own pull request.
With one active identity, a rule requiring a GitHub-native approving
review before merge is not a safety check — it's a permanent deadlock.

## 2. What changed, and why

**Original setup:** the `main` branch protection rule required 1
approving review before merge, in addition to a passing Vercel status
check.

**Problem discovered:** during Iteration 3 Batch 1 (2026-08-05), the
first PR under this workflow could not clear its merge requirements.
Arthur's own account authored the PR; GitHub silently downgrades any
review submitted by the PR author to a comment, never an approval. The
sidebar kept reporting "at least 1 approving review is required"
indefinitely — there is no account available to satisfy it.

**Resolution:** the "Require approvals" rule was removed from the
`main` branch protection rule. Nothing else changed. See §3 for the
exact current configuration.

**What replaces reviewer approval:** a human review step still exists —
it just happens against the live Vercel Preview deployment instead of
inside GitHub's review UI. See §4.

## 3. Current branch protection on `main`

Verified directly in repo settings on 2026-08-05:

**Enabled:**
- Require a pull request before merging
- Require status checks to pass before merging — required check: **Vercel**
- Require branches to be up to date before merging

**Disabled:**
- Require approvals *(removed 2026-08-05 — see §2)*
- Dismiss stale approvals, code owner review, restrict review dismissal, bypass actors, require approval of most recent push *(all were already off; moot now that approvals aren't required)*
- Conversation resolution, signed commits, linear history, merge queue, deployment success, lock branch, "do not allow bypassing," restrict push, force pushes, deletions

**Repo-level merge settings (Settings → General, unaffected by the above):**
- Allow squash merging: **on** (only method enabled — merge commits and rebase are both off)
- Allow auto-merge: **on**
- Automatically delete head branches: **on**

Direct pushes to `main` are still impossible — a PR is still mandatory,
and it still cannot merge until the Vercel check passes. The only thing
that changed is *who* has to say "this is good to ship."

## 4. The approved workflow

```
Request
  ↓
Branch + PR opened, auto-merge (squash) enabled
  ↓
Vercel Preview builds and posts its URL on the PR
  ↓
Approve or Reject   ← human decision, made by reviewing the live Preview
  ↓                    (not a GitHub "Approve" click — see §2)
Auto-Merge fires automatically once the Vercel check passes
  ↓
Production deployment (Vercel promotes the merge commit)
  ↓
Verify live on www.expertsmarketing.com
```

Approval is a judgment call made by looking at the actual rendered
Preview, not a rubber-stamp on a diff. That review step is not
optional — it just isn't enforced by GitHub's reviewer mechanism
anymore, so it's on the person driving the change (Arthur, or an agent
acting on his explicit instruction) to actually look before saying
"proceed."

See `WEBSITE_CHANGE_WORKFLOW.md` for the step-by-step procedure and
`ARTHUR_QUICK_REFERENCE.md` for a condensed cheat sheet.

## 5. Revisit triggers

Re-open this decision if any of the following happen:
- A second person becomes an active GitHub contributor (Christian or
  otherwise) — at that point, re-enabling "Require approvals" with that
  person as reviewer is straightforward and adds real safety back.
- The team wants enforcement that a Preview was actually reviewed
  (today that step is trusted, not verified by tooling).
