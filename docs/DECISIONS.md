# Decisions Log — Experts Marketing Website

Status: ACTIVE. First version: 2026-08-05. This is the permanent record
of major architectural and operational decisions for this project —
what was decided, why, what it changed, and whether it's still in
effect. New decisions should be appended here going forward, not left
to live only in chat history.

Each entry lists a **Source** — the document and section this entry
was extracted from. Where the five source documents don't record an
exact date, that's stated plainly rather than guessed.

---

## 1. Migration from Plesk to GitHub

- **Date:** Not dated in the five documents analyzed for this log
  (predates 2026-08-03). The full historical record lives in
  `GO_LIVE_PLAN.md`, `MIGRATION_PLAN.md`, and `GITHUB_SETUP_PLAN.md` —
  none of which were in scope for this document.
- **Decision:** Retire Plesk as the code repository. Make the
  `expertsmarketing-website` repository, under the `ExpertsMarketing`
  GitHub organization, the single source of truth for the site's code.
- **Reason:** Plesk's direct-file-edit model had no usable version
  history, no branching, no review step, and no safe way to introduce
  AI-assisted editing. GitHub is referenced throughout the operating
  documents as the settled foundation everything else is built on.
- **Impact:** Established `main` as the only branch Vercel treats as
  Production, branch protection (no direct pushes, Pull Requests
  required), and the one-branch-per-change convention the rest of the
  operating model depends on.
- **Status:** Complete and active — foundational to every later
  decision in this log.
- **Source:** `OPERATING_MODEL.md` §2; `WEBSITE_CHANGE_WORKFLOW.md`
  intro; `ARTHUR_QUICK_REFERENCE.md` closing line.

---

## 2. Migration from Plesk to Vercel

- **Date:** Not dated in the five documents analyzed for this log
  (same era as Decision 1). See `VERCEL_SETUP_PLAN.md` / `GO_LIVE_PLAN.md`.
- **Decision:** Retire Plesk as the hosting/deployment platform. Make
  Vercel the exclusive deployment target for `www.expertsmarketing.com`,
  connected to the GitHub repository via Vercel's native Git
  integration.
- **Reason:** Vercel's Git integration provides automatic Preview
  Deployments per branch/PR and automatic Production deploys on merge
  to `main` — infrastructure a manual FTP/Plesk upload model could not
  offer, and a prerequisite for the Preview-before-Production principle
  the rest of the model depends on.
- **Impact:** Production now deploys exclusively from `main`, never
  from a branch or a manual upload — one of the four non-negotiable
  requirements the operating model is built around.
- **Status:** Complete and active.
- **Source:** `OPERATING_MODEL.md` §0 (Goal #3), §4.

---

## 3. Adoption of Vercel Preview Deployments

- **Date:** Established as part of the original operating baseline,
  predating the 2026-08-05 changes below.
- **Decision:** Every pushed branch and every open Pull Request
  automatically gets its own unique Preview Deployment URL, with no
  manual trigger required.
- **Reason:** Makes visual review possible before a change reaches
  live visitors. Directly satisfies Goal #1 of the operating model:
  "Every change produces a Preview Deployment before anything else
  happens."
- **Impact:** Preview review became the mandatory first checkpoint of
  every change, and the Vercel deployment check that branch protection
  depends on. Preview links are retained after merge, useful for
  later "what did this look like before" comparisons.
- **Status:** Active, unchanged since adoption.
- **Source:** `OPERATING_MODEL.md` §0, §4; `WEBSITE_CHANGE_WORKFLOW.md`
  §4.

---

## 4. Adoption of GitHub Auto-Merge

- **Date:** 2026-08-05.
- **Decision:** Enable GitHub's repo-level "Allow auto-merge" setting,
  so Pull Requests merge automatically once required conditions are
  met, instead of requiring someone to click "Merge."
- **Reason:** Part of reducing Arthur's operational workload to three
  actions only (request, review Preview, approve/reject) — auto-merge
  removes the manual merge click after approval.
- **Impact:** Initially enabled at Pull-Request-creation time. This
  ordering was identified the same day as a safety gap and corrected —
  see Decision 6.
- **Status:** Active, with the corrected timing from Decision 6.
- **Source:** `OPERATING_MODEL.md` §10 Change Log (2026-08-05 entry).

---

## 5. Removal of the "Require Approvals" Branch Protection Rule

- **Date:** 2026-08-05 (later the same day as Decision 4).
- **Decision:** Turn off GitHub's required-reviewer branch protection
  rule on `main`.
- **Reason:** Experts Marketing operates under a single shared GitHub
  identity (`adeperthuis@gmail.com`). GitHub structurally refuses to
  let an account approve its own Pull Request. With required-review
  on, every PR authored under that identity was permanently
  unapprovable — confirmed by a real incident (see
  `LESSONS_LEARNED.md` #1) where GitHub silently downgraded Arthur's
  own review to a comment and the branch-protection sidebar reported
  the requirement as unmet indefinitely.
- **Impact:** Removed a structural (GitHub-enforced) approval gate.
  This is explicitly documented as a real reduction in guarantee, not
  a neutral change — see the "⚠️ Risk callout" in `OPERATING_MODEL.md`
  §5.
- **Status:** Active. Explicitly flagged to revisit if a second active
  GitHub contributor is ever added — re-enabling this rule with that
  person as reviewer would restore a platform-enforced gate.
- **Source:** `OPERATING_MODEL.md` §1, §5, §9, §10.

---

## 6. Adoption of Explicit Chat-Based Approval

- **Date:** 2026-08-05, the same change as Decision 5 — a paired
  decision, not a separate event.
- **Decision:** Arthur's explicit message in chat ("approved,"
  "reject this," or similar) becomes the actual authorization to ship
  a change, replacing the GitHub review click. Auto-merge is
  deliberately withheld until that message arrives, never enabled at
  Pull-Request-creation time.
- **Reason:** A replacement gate was needed once GitHub-native review
  became structurally impossible (Decision 5). The auto-merge-timing
  change specifically closes a real vulnerability: with required-review
  off, a PR with auto-merge enabled at creation would merge unattended
  the instant the Vercel check passed, with no human wait at all.
- **Impact:** The approval gate changed from structural
  (platform-enforced) to procedural (Claude-enforced) — an explicitly
  accepted, documented trade-off. Arthur's responsibilities were
  simultaneously reduced to exactly three actions: request the change,
  review the Preview, explicitly approve or reject in chat.
- **Status:** Active. Per `WEBSITE_CHANGE_WORKFLOW.md`'s "Validated
  Workflow" section, the chat-gated auto-merge sequence specifically
  had not yet had its own dedicated end-to-end test as of the last
  update to that document — an open item, not a closed one.
- **Source:** `OPERATING_MODEL.md` §5, §7, §9; `WEBSITE_CHANGE_WORKFLOW.md`
  §5, §6, "Validated Workflow"; `ARTHUR_QUICK_REFERENCE.md`.

---

## 7. Documentation Reconciliation Process

- **Date:** 2026-08-05.
- **Decision:** Merge two independently-developed versions of
  `OPERATING_MODEL.md`, `WEBSITE_CHANGE_WORKFLOW.md`, and
  `ARTHUR_QUICK_REFERENCE.md` — one merged to GitHub `main` via PR #4,
  one developed locally — into single canonical documents, using the
  local versions as the foundation.
- **Reason:** Parallel work had produced two forks of the same three
  filenames without either side being aware of the other's changes
  landing on GitHub. Left unreconciled, GitHub's copy would have been
  stale relative to the actual, more current operating model.
- **Impact:** Five GitHub-only technical items (identity model table,
  self-review-downgrade incident detail, mixed line-ending warning,
  cache-busted verification technique, time-boxed GitHub-web-UI note)
  were merged into `OPERATING_MODEL.md`; two secondary mentions were
  added to `WEBSITE_CHANGE_WORKFLOW.md`; `ARTHUR_QUICK_REFERENCE.md`
  was kept unchanged to protect its conciseness goal.
- **Status:** Complete — merged via Pull Request #7 on 2026-08-05.
- **Source:** `OPERATING_MODEL.md` §10 Change Log (reconciliation
  entry); `DOCUMENTATION_RECONCILIATION_PLAN.md`.

---

## 8. Claude Code Migration Decision

- **Date:** Plan and Phase 1 setup guide approved 2026-08-05.
- **Decision:** Replace Claude in Chrome browser automation with
  Claude Code, running locally on Arthur's machine, as the execution
  mechanism for the already-validated workflow. The workflow itself
  does not change — only how the branch/commit/push/PR/auto-merge
  steps are carried out. Approved design: Arthur's machine as the sole
  installation to start; a dedicated, scoped, expiring GitHub PAT
  (Option B) rather than reuse of Arthur's personal login session; no
  Vercel credential; a `CLAUDE.md` project-instructions file committed
  to the repo; command-scoped Claude Code configuration; Cowork
  retained as a documented fallback.
- **Reason:** Browser automation is slow (roughly 1.5–3+ minutes for a
  simple one-file change) and fragile — coupled to GitHub's current
  page structure, and has already required adjustment during earlier
  phases of this project. A local CLI is estimated at roughly 10–15
  seconds per simple change, using stable API/CLI contracts instead of
  screenshot/click cycles.
- **Impact:** Introduces a real, persistent GitHub credential on
  Arthur's machine where none existed before — a materially new risk
  profile requiring ongoing credential hygiene (rotation, secure
  storage, revocation on machine compromise) that a credential-less
  browser session didn't require.
- **Status:** Plan and Phase 1 guide approved; Phase 1 execution
  (installation, token creation, `CLAUDE.md`, command-scoping),
  Phase 2 validation, local repository cleanup, and the first live
  approve/reject test under Claude Code all remain pending as of the
  last update to these documents.
- **Source:** `CLAUDE_CODE_MIGRATION_PLAN.md` (full document, esp. §3,
  §5, §8, §12); `CLAUDE_CODE_PHASE1_SETUP_GUIDE.md` (status line,
  Phase 1 exit checklist).
