# Architecture History — Experts Marketing Website

Status: ACTIVE. First version: 2026-08-05. This is the permanent
timeline record of how this project's architecture evolved. Each stage
below is deliberately brief — full rationale lives in `DECISIONS.md`,
full incident/observation detail lives in `LESSONS_LEARNED.md`. This
document exists to answer "how did we get here, in what order" at a
glance; follow the cross-references for the "why" and "what went
wrong."

```
Stage 0: Original Plesk Architecture
  ↓
Stage 1: GitHub Migration
  ↓
Stage 2: Vercel Migration
  ↓
Stage 3: Preview Deployment Workflow
  ↓
Stage 4: Auto-Merge Workflow
  ↓
Stage 5: Approval Workflow Redesign
  ↓
Stage 6: Documentation Reconciliation
  ↓
Stage 7: Claude Code Migration Preparation (current stage)
```

---

## Stage 0: Original Plesk Architecture

- **State:** Static HTML/CSS/JavaScript website hosted on Plesk, edited
  and deployed via direct file changes on the server (no branching, no
  review step, no automated preview). Contact form connected to Google
  Apps Script; leads stored in Airtable; email notifications sent to
  `arthur@expertsmarketing.com`.
- **Reason for change:** Direct file edits on Plesk offered no
  preview-before-production step, no reliable rollback mechanism, no
  single source of truth, and no safe way to introduce AI-assisted
  editing — all objectives of this project.
- **Outcome:** Retired. `WEBSITE_CHANGE_WORKFLOW.md` states plainly:
  "the old way of editing files directly on Plesk... no longer
  exists." The contact form → Google Apps Script → Airtable → email
  integration was preserved unchanged throughout every later stage —
  none of the migrations below touched that chain.
- **Detail:** `DECISIONS.md` #1, #2.

---

## Stage 1: GitHub Migration

- **State:** Repository `expertsmarketing-website` created under the
  `ExpertsMarketing` GitHub organization; GitHub established as the
  single source of truth for the site's code; `main` protected as the
  only branch Vercel treats as Production.
- **Reason for change:** Needed a canonical, versioned, reviewable home
  for the site's code before any deployment or review workflow could
  be built on top of it.
- **Outcome:** Branch protection established (no direct pushes, Pull
  Requests required), and the one-branch-per-change convention that
  every later stage depends on.
- **Detail:** `DECISIONS.md` #1.

---

## Stage 2: Vercel Migration

- **State:** Vercel adopted as the hosting/deployment platform,
  connected to the GitHub repository via Vercel's native Git
  integration, replacing Plesk's server entirely.
- **Reason for change:** Vercel's Git integration provides automatic
  Preview Deployments per branch/PR and automatic Production deploys
  on merge to `main` — infrastructure a manual-upload model could not
  offer.
- **Outcome:** Production deploys exclusively from `main`, never from
  a branch or a manual upload — one of the four non-negotiable
  requirements the operating model is built around.
- **Detail:** `DECISIONS.md` #2.

---

## Stage 3: Preview Deployment Workflow

- **State:** Every pushed branch and every open Pull Request
  automatically gets its own unique Preview Deployment URL, posted
  automatically as a GitHub status check / PR comment.
- **Reason for change:** Makes visual review possible before a change
  reaches live visitors — Goal #1 of the operating model.
- **Outcome:** Became the mandatory first checkpoint of every change.
  Validated end-to-end: repository discovered automatically, branch
  and Pull Request created, Preview generated and reviewed, and a
  deliberate rejection test confirmed PR-close + branch-delete +
  production-unchanged.
- **Detail:** `DECISIONS.md` #3; `LESSONS_LEARNED.md` #5.

---

## Stage 4: Auto-Merge Workflow

- **State (2026-08-05):** GitHub's repo-level "Allow auto-merge"
  setting enabled, initially turned on at Pull-Request-creation time.
- **Reason for change:** Part of reducing Arthur's operational
  workload to three actions only — auto-merge removes the manual
  merge click after approval.
- **Outcome:** Enabling auto-merge at PR-creation time (rather than
  after approval) was identified the same day as a safety gap — with
  GitHub's required-review setting also being removed around the same
  time (Stage 5), a PR with auto-merge already on would merge the
  instant the Vercel check passed, with no wait for human review at
  all. Corrected in Stage 5.
- **Detail:** `DECISIONS.md` #4.

---

## Stage 5: Approval Workflow Redesign

- **State (2026-08-05, same day as Stage 4):** GitHub's "Require
  approvals" branch protection rule removed; replaced with explicit
  chat-based approval; auto-merge timing corrected to be enabled only
  after that chat approval, never at PR-creation.
- **Reason for change:** Experts Marketing operates under a single
  shared GitHub identity, and GitHub structurally refuses to let an
  account approve its own Pull Request — confirmed by a real incident
  where the first PR under the required-approval rule could never be
  approved by anyone.
- **Outcome:** The approval gate shifted from structural
  (GitHub-enforced) to procedural (Claude-enforced) — an explicitly
  accepted, documented reduction in guarantee. Arthur's responsibilities
  settled at exactly three actions: request, review Preview, explicitly
  approve or reject in chat.
- **Detail:** `DECISIONS.md` #5, #6; `LESSONS_LEARNED.md` #1.

---

## Stage 6: Documentation Reconciliation

- **State (2026-08-05):** Two independently-developed versions of the
  core operating documents existed — one on GitHub `main` (via PR #4),
  one developed locally. Reconciled into single canonical versions,
  using the local versions as the foundation, and merged to `main` via
  Pull Request #7.
- **Reason for change:** Parallel development had let GitHub's
  documentation and the actively-evolving local documentation drift
  apart. The project needed one source of truth for its own
  documentation, matching the "GitHub is the source of truth"
  principle already applied to code.
- **Outcome:** `OPERATING_MODEL.md`, `WEBSITE_CHANGE_WORKFLOW.md`, and
  `ARTHUR_QUICK_REFERENCE.md` reconciled and merged. GitHub now holds
  the canonical version of both the website's code and its own
  operating documentation.
- **Detail:** `DECISIONS.md` #7; `LESSONS_LEARNED.md` #6.

---

## Stage 7: Claude Code Migration Preparation (Current Stage)

- **State (2026-08-05, plan approved; not yet executed):** Migration
  plan and Phase 1 setup guide written for moving execution from
  Claude in Chrome browser automation to Claude Code running locally
  on Arthur's machine — a dedicated scoped GitHub PAT, a `CLAUDE.md`
  project-instructions file, and command-scoped configuration.
- **Reason for change:** Browser automation is slow and fragile
  compared to a local CLI with direct `git`/`gh` access. The
  underlying validated workflow itself does not change — only its
  execution mechanism.
- **Outcome (as of the last update to these documents):** Plan and
  Phase 1 guide approved. Phase 1 execution (installation, token
  creation, `CLAUDE.md`, command-scoping), Phase 2 validation, local
  repository cleanup, and the first live approve/reject test under
  Claude Code all remain pending. This document should be updated with
  a Stage 8 entry once Phase 1 is actually executed — not before.
- **Detail:** `DECISIONS.md` #8; `LESSONS_LEARNED.md` #4.

---

*This document is a companion to `DECISIONS.md` (decision rationale
and status) and `LESSONS_LEARNED.md` (incidents and observations).
Update all three together when a new stage is reached — a timeline
entry without its corresponding decision and lesson-learned detail (or
vice versa) leaves this knowledge base inconsistent.*
