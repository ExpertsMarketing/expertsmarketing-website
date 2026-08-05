# Long-Term Operating Model — AI-Assisted Website Changes

Status: ACTIVE. Last verified: 2026-08-05. This describes how the site
is operated for all ongoing changes after the Go-Live cutover
(`GO_LIVE_PLAN.md`). It supersedes the review-workflow assumptions in
`VERCEL_SETUP_PLAN.md` and `GITHUB_SETUP_PLAN.md`, which predate the
2026-08-05 approval-model revision described in Section 5.

Primary technical owner (GitHub/Vercel account of record): adeperthuis@gmail.com
Business/project email (Experts Marketing SL): arthur@expertsmarketing.com

## 0. Goal

Arthur describes a website change in plain language (in chat, to
Claude). Claude turns that into a real code change, routed through
GitHub and Vercel exactly like any other change to this repository —
never as a special "AI shortcut" that skips review. The four hard
requirements this model is built around:

1. Every change produces a Preview Deployment before anything else happens.
2. A human (Arthur, or Christian when authorized) explicitly approves
   before anything reaches production. As of 2026-08-05 this approval
   is given as an explicit chat message rather than a GitHub review
   click (Section 5 explains why, and what changed).
3. Production only ever deploys from `main` — never from a branch,
   never from a manual upload.
4. Rollback is always available and fast.

## 1. Identity Model

The project is operated by a single GitHub identity:

| Role | Account | Notes |
|---|---|---|
| GitHub org owner / sole active contributor | `adeperthuis` (Arthur, adeperthuis@gmail.com) | Authors every commit and PR |
| Vercel team/project owner | adeperthuis@gmail.com | Same person, same account |
| Additional collaborator | Christian | Invited to Vercel for migration/support; not currently an active GitHub contributor |

This matters because it's the reason the workflow in this document
looks the way it does: GitHub will not let an account approve its own
Pull Request. With one active GitHub identity, a rule requiring a
GitHub-native approving review before merge isn't a safety check — it
would be a permanent deadlock, since there is no second account
available to satisfy it. Section 5 covers what happened when this was
tried, and what replaced it.

**Revisit trigger:** if a second person becomes an active GitHub
contributor (Christian or otherwise), re-enabling GitHub's "Require
approvals" with that person as reviewer becomes possible again, and
would restore a platform-enforced approval gate — see Section 9's
"Revisit if" note.

## 2. Git Branching Strategy

Unchanged from `GITHUB_SETUP_PLAN.md` §3–4, restated here as the
operating baseline (not a new decision, just the standing rule this
whole model depends on):

- `main` is the only branch Vercel treats as Production. It is
  protected: no direct pushes, from anyone, including Arthur and
  including Claude. Recommend enabling **"Do not allow bypassing the
  above settings"** (include administrators) in GitHub branch
  protection, so this is structural rather than a habit anyone could
  accidentally break.
- One short-lived branch per requested change, named descriptively:
  `change/homepage-headline`, `fix/footer-broken-link`,
  `feature/faq-section`. No long-lived `develop`/`staging` branch —
  Vercel's per-branch Preview Deployments already provide that.
- Branches are deleted after merge (enable GitHub's "Automatically
  delete head branches" repo setting) to keep the branch list clean.
- Squash-merge only (already decided in `GITHUB_SETUP_PLAN.md` §4) —
  every change on `main` is exactly one commit, which is what makes
  git-revert rollback simple and reliable.

## 3. GitHub Workflow (per change)

**Before editing anything, check line endings.** This repository has a
mixed convention: `index.html` and `assets/styles.css` use CRLF;
`privacy.html`, `services/seo.html`, and `services/geo-aeo.html` use
LF. Other files haven't been audited — check with `cat -A` (or
equivalent) before assuming. Editing with the wrong line ending turns a
small, reviewable change into a full-file diff and makes the PR
effectively unreviewable. This is a real, recurring risk, not a
one-time gotcha — check it every time, not just the first time.

**Current implementation mechanism (time-boxed note):** as of this
writing, no git CLI or token access is assumed for Claude's own
execution environment — changes are committed through GitHub's web
upload/edit UI. This is expected to change once
`CLAUDE_CODE_MIGRATION_PLAN.md` Phase 1–2 are complete and Claude Code
is executing locally with real git/`gh` access. **Update this note when
that happens** rather than leaving it stale — it's a description of
current mechanics, not a permanent architectural constraint.

1. Claude creates a new branch off the latest `main` for the
   requested change.
2. Claude makes the edit(s) — HTML/CSS/asset changes, never touching
   `vercel.json`'s redirect/header logic without calling that out
   explicitly, since that file carries the catch-all warning from
   `PROJECT_ARCHITECTURE.md` §4.
3. Claude commits with a descriptive, present-tense message
   (`GITHUB_SETUP_PLAN.md` §7 convention) and pushes the branch.
4. Claude opens a pull request against `main`, with a short description
   of what changed and why, and — for anything touching business
   functionality (contact form, redirects, integrations) — a one-line
   note on what was tested.
5. Claude does **not** enable auto-merge yet. The PR sits open, with
   Vercel's Preview and status check running, until Arthur (or
   Christian) reviews it.
6. Arthur reviews the Preview and replies in chat with an explicit
   approval or rejection (Section 5 — this is now the actual gate,
   since GitHub's own required-review setting is off).
7. **Only after that explicit chat approval**, Claude enables GitHub's
   **auto-merge** on the PR (repo setting "Allow auto-merge," enabled
   2026-08-05). This does not give Claude merge rights — it tells
   GitHub to merge the PR itself, automatically, once the remaining
   conditions are met.
8. GitHub branch protection blocks the merge until the Vercel
   deployment check passes and the branch is up to date with `main`
   (there is no required-reviewer condition anymore — see Section 5
   for why). Since the Vercel check has usually already passed by the
   time Arthur approves, the merge typically fires within seconds of
   auto-merge being enabled.
9. GitHub performs the squash merge. Merge to `main` automatically
   triggers a Vercel Production deployment, and the merged branch is
   auto-deleted (existing "Automatically delete head branches"
   setting).
10. Claude verifies the required checks passed, confirms the
    production deployment succeeded, and checks the live site using a
    **cache-busted request** (e.g. fetching with a `?cachebust=<timestamp>`
    query parameter and `no-store` caching, in an actual browser
    context) rather than a plain fetch — CDN/edge caching can return
    stale content immediately after a deploy and produce a false
    negative otherwise. Claude then reports back that the change is
    live.

If Arthur rejects instead of approving: Claude closes the PR and
deletes the branch. Auto-merge is never enabled, so nothing was ever
one Vercel-check-pass away from shipping.

## 4. Vercel Preview Workflow (per change)

- Every pushed branch and every open PR gets its own unique Preview
  Deployment URL automatically — no manual trigger needed, this is
  Vercel's standard Git integration behavior (`VERCEL_SETUP_PLAN.md`
  §2).
- Vercel posts the Preview URL as a GitHub status check / PR comment
  automatically.
- This is where all visual review happens. Arthur (or Christian) opens
  the live Preview URL in a browser and checks the actual change before
  approving anything — never approve based on a description alone.
- Preview Deployments are retained in Vercel's history after merge,
  useful for comparing "what did this look like before" even after the
  branch is deleted.
- **Note on Preview exposure:** since the repository is public, Preview
  URLs are unauthenticated-but-unguessable (random subdomain hash).
  For a marketing site this is a low-severity, accepted risk — nothing
  in Preview is more sensitive than what's already public in the repo.
  If this ever changes (e.g. draft content that shouldn't leak), Vercel
  offers Preview Deployment password/SSO protection as a paid add-on —
  not needed at current scope, worth remembering if scope changes.

## 5. Human Approval Gate — Revised (2026-08-05): Chat Replaces GitHub Review

**This section changed materially on 2026-08-05 and is worth reading
in full, including the risk callout at the end.**

`GITHUB_SETUP_PLAN.md` §4 originally deferred requiring PR approvals,
and the 2026-08-03 draft of this document recommended turning that
requirement on (previous version of this section, preserved in git
history). It was turned on. It has since been turned back **off**, for
a concrete operational reason, not a change of philosophy:

**Why it was removed.** Experts Marketing currently operates with a
single GitHub identity (`adeperthuis@gmail.com`, Section 1). GitHub
does not allow an account to approve a Pull Request it authored —
self-approval is blocked at the platform level. The problem wasn't
theoretical: during Iteration 3 Batch 1 (2026-08-05), the first PR
opened under the required-approval rule could not clear its merge
requirements. Arthur's own account had authored the PR; GitHub
silently downgraded any review he submitted to a comment, never an
approval, and the PR sidebar kept reporting "at least 1 approving
review is required" indefinitely — there was no account available to
satisfy it. With Claude opening every PR under that one identity, the
required-approval rule meant no PR could ever be approved by anyone:
every change would sit blocked forever. Rather than leave the workflow
deadlocked, "Require approvals" was turned off in GitHub's branch
protection for `main`.

**What replaced it.** Approval now happens as an explicit message in
chat — "approved," "reject this," or similar in Arthur's own words —
and Claude treats that message as the trigger for one specific action:
enabling GitHub's auto-merge on the PR (Section 3, steps 5–7).
Critically, **auto-merge is never enabled at PR-creation time anymore**
— only after the chat approval arrives. That ordering is what makes
the chat message function as a real gate: since the only remaining
GitHub-side conditions are the Vercel check and branch-up-to-date, a
PR with auto-merge already on would merge the instant the check
passes, with no wait for anyone's review. Delaying auto-merge until
after approval is not an optional nicety — it's the entire mechanism
that makes this safe.

This still sits on top of, not instead of, the Preview visual review
in Section 4 — Preview lets a human *see* the change; the chat message
is what *authorizes* it to ship.

**⚠️ Risk callout — read this part.** Before 2026-08-05, the approval
gate was enforced by GitHub itself: no chat message, no promise, no
policy could make a PR merge without a recorded platform-level review.
That is no longer true. The gate now depends on Claude correctly
withholding one action (enabling auto-merge) until it has received an
unambiguous chat approval — a **procedural control**, not a
**structural** one. Nothing on GitHub's side would stop a merge if
Claude enabled auto-merge early, whether by mistake or by
misinterpreting an ambiguous message as approval. This is a real,
documented reduction in guarantee, accepted specifically to solve the
single-identity deadlock — not something to lose sight of as "basically
the same as before." Mitigations in place: Claude is instructed to
treat "opened a Preview" and silence as *not* approval, to require an
unambiguous affirmative message, and to never enable auto-merge
speculatively. The most durable fix, if it matters enough later, is
restoring a second reviewing identity — see the "Revisit if" note in
Section 9 (and Section 1).

Arthur's job remains reduced to exactly three things: request the
change, review the Preview, explicitly approve or reject in chat.
Everything after approval — enabling auto-merge, the merge itself,
production deployment, deployment confirmation, post-deploy
validation, branch cleanup — happens without further action from
Arthur or Christian.

## 6. Rollback Procedures

Same layered model as `MIGRATION_PLAN.md` §7 and `GO_LIVE_PLAN.md` §4,
restated for steady-state operation after go-live:

1. **Fastest — Vercel instant rollback.** Vercel dashboard →
   Deployments → select the last known-good production deployment →
   "Promote to Production." Seconds, no git action needed. Correct
   response when something is visibly broken right now and speed
   matters more than a clean history.
2. **Git revert.** Because every change on `main` is a single squash
   commit, `git revert <commit>` on the specific bad change (opened as
   its own PR, reviewed and merged the same way as any other change)
   cleanly undoes it and triggers a new, correct production deployment.
   Preferred when there's time to do it properly, since it keeps git
   history accurate and revertible again in the future.
3. **DNS revert** — out of scope here (this only applies to a
   platform-level Vercel outage, not a content change gone wrong); see
   `GO_LIVE_PLAN.md` §4 for that tier.

Rollback triggers to watch for in steady-state: a merged change breaks
a page, breaks the contact form, introduces a visual regression not
caught in Preview, or unexpectedly touches `vercel.json`'s redirect
behavior.

## 7. Why Claude Doesn't Hold Merge Rights (Updated 2026-08-05 — Read With Section 5)

Enabling GitHub's auto-merge does **not** give Claude push or merge
access. It's a GitHub-native feature Claude switches on per-PR; GitHub
then merges automatically once its own remaining conditions (Vercel
check, branch up to date) are satisfied. Two structural facts are
unchanged from before:

- Per `PROJECT_ARCHITECTURE.md` §6 and `GITHUB_SETUP_PLAN.md` §8,
  AI-assisted edits are never configured with direct push rights to
  `main`. Claude's role is: branch, edit, commit, push branch, open PR,
  and — only after chat approval — enable auto-merge. The actual merge
  is always performed by GitHub, not by a Claude-initiated push.
- Claude's sandbox environment has no push credentials at all — every
  push in this project has required Arthur or Christian to execute it
  from their own authenticated machine, or Claude has committed
  through GitHub's web upload UI (Section 3) under Arthur's logged-in
  session.

**What is no longer true, and shouldn't be overstated:** before
2026-08-05, this section could honestly say "no path from 'Claude
thinks this is ready' to production without a human's explicit,
GitHub-enforced approval" — that was a structural guarantee, enforced
by GitHub's required-review setting independent of anything Claude did
or said. With that setting off (Section 5), the only thing standing
between "Claude opened a PR" and "it ships" is Claude correctly waiting
for an explicit chat approval before enabling auto-merge. That is a
real safeguard — Claude is instructed to hold the line on it, and doing
otherwise would be a clear violation of this operating model — but it
is enforced by Claude's behavior, not by GitHub. Readers of this
document should understand the difference: **Claude not holding push
rights still means Claude cannot force a merge through directly, but
it no longer means a human's approval is independently verified by the
platform before merge happens.** See Section 5's risk callout for what
mitigates this and what would restore the stronger guarantee.

## 8. Examples: Natural-Language Change Requests

| Arthur says | What Claude does |
|---|---|
| "Update the homepage headline to say 'AI-era growth marketing, built for revenue.'" | Branch `change/homepage-headline` → edit the headline text in `index.html` → commit → push → open PR → share Preview link |
| "Fix the broken link to the SEO service page in the footer." | Branch `fix/footer-seo-link` → correct the `href` in the shared footer markup → PR → Preview link |
| "Add a short FAQ section to the About page answering 'how long until we see results?'" | Branch `feature/about-faq` → add a new section to `about.html` → PR → Preview link, called out as a new content section for visual review |
| "Change the 'Talk to us' button text to 'Book a call' site-wide." | Branch `change/cta-copy` → update the CTA string everywhere it appears across pages → PR noting every file touched, since it's a multi-page change |
| "Add a new blog post about [topic]." (future, per `PROJECT_ARCHITECTURE.md` §8) | Branch `feature/blog-<slug>` → new page under `/blog/` + `sitemap.xml` update → PR → Preview link |
| "Something looks broken on the contact page, can you check?" | Claude investigates on the **live production** site (read-only), reports findings, and — if a fix is needed — proposes it as a normal branch/PR/Preview cycle, not a direct edit |

In every case, the shape is identical: branch → edit → PR (auto-merge
withheld) → Preview → human review and **explicit chat approval** →
auto-merge enabled → automatic merge → automatic production deploy →
Claude confirms it's live. There is no "small change" exception that
skips this — the overhead of a branch and PR is trivial compared to the
cost of an unreviewed change reaching a live business site. Arthur's
part in that chain is only two steps: review the Preview, and
explicitly approve or reject in chat.

## 9. Recommended Operating Model (Summary)

For a small marketing site with a two-person team (Arthur, Christian)
and AI-assisted editing, the safest model is the one already mostly in
place, with one tightening:

- **Keep:** one branch per change, squash-merge only, Vercel Preview on
  every branch/PR automatically, production only deploys from `main`,
  Plesk-style direct/manual edits permanently retired, GitHub auto-merge
  repo-wide (but withheld until chat approval — Section 3).
- **Removed (2026-08-05):** the required-GitHub-approval branch
  protection rule, because a single shared GitHub identity made
  self-approval impossible and would have deadlocked every PR forever
  (Section 1, Section 5).
- **Added (2026-08-05):** explicit chat-based approval as the
  replacement gate. This is a **weaker guarantee** than the
  GitHub-enforced review it replaced (Section 5's risk callout) — it's
  the pragmatic choice given a single-identity constraint, not a
  strictly-better redesign. Arthur's workload is unchanged at three
  actions: request, review Preview, explicitly approve/reject in chat;
  everything after that approval (auto-merge, production deploy,
  deployment confirmation, branch cleanup) is automatic.
- **Do not add (over-engineering for this scale):** multiple
  environments beyond Preview/Production, mandatory CI test suites, a
  full staging database, or a formal change-approval board. All of
  these solve problems a 9-page static marketing site with no backend
  doesn't have. `GITHUB_SETUP_PLAN.md` §10 already lists the two
  optional CI additions (broken-link checking, HTML/accessibility
  linting) worth considering later — that ceiling is enough for this
  project's scale.
- **Revisit if:** the team grows past Arthur + Christian, the blog
  expansion (`PROJECT_ARCHITECTURE.md` §8) introduces enough volume
  that the current model feels too loose, or a second individual
  GitHub identity is ever added (per the original `GITHUB_SETUP_PLAN.md`
  §8 access model and Section 1 above) — at which point re-enabling
  GitHub-required approval becomes possible again, and would restore
  the stronger, platform-enforced guarantee described in the
  pre-2026-08-05 version of Section 5.

## 10. Change Log

| Date | Change |
|---|---|
| 2026-08-03 | Initial operating model drafted, prior to DNS cutover. Recommends tightening PR-approval requirement from "deferred" (`GITHUB_SETUP_PLAN.md` §4) to "required" now that AI-assisted changes will be routine. No GitHub/Vercel settings changed by this document. |
| 2026-08-05 | Enabled GitHub's repo-level "Allow auto-merge" setting. Updated the model so approval is Arthur's final manual action — GitHub auto-merges once approval + the Vercel check pass, Vercel deploys automatically, and Claude confirms/validates the live deployment and reports back. Arthur's ongoing responsibilities reduced to: request the change, review the Preview, approve or reject. Claude does not hold merge or push rights; §7 explains why this still holds true with auto-merge enabled. |
| 2026-08-05 (later) | Removed the "Require approvals" branch protection rule on `main` — single shared GitHub identity made self-approval impossible and would have deadlocked every PR. Replaced with explicit chat-based approval (§5, revised) and reordered the workflow so auto-merge is enabled only after that chat approval, never at PR-creation (§3, revised). §7 revised to acknowledge this is now a procedural (Claude-enforced) gate rather than a structural (GitHub-enforced) one — a real, documented reduction in guarantee, accepted to solve the single-identity deadlock. §9 updated accordingly; restoring a second GitHub identity would allow re-enabling the stronger, platform-enforced gate later. |
| 2026-08-05 (reconciliation) | Merged in technical detail from a parallel version of this document that had been independently drafted and merged to `main` via PR #4 while this version was being developed locally: new Section 1 (Identity Model table), the specific self-review-downgrade incident detail merged into Section 5, the mixed line-ending warning and cache-busted verification technique added to Section 3, and a time-boxed note on the current GitHub web-upload implementation mechanism. All prior sections renumbered by +1 to accommodate the new Section 1. No content removed; see `DOCUMENTATION_RECONCILIATION_PLAN.md` for the full reconciliation record. |
