# Lessons Learned — Experts Marketing Website

Status: ACTIVE. First version: 2026-08-05. This is the permanent
record of operational incidents, observations, and standing risks for
this project, and what changed as a result. Companion document to
`DECISIONS.md` (what was decided) and `ARCHITECTURE_HISTORY.md` (the
timeline) — entries here focus on *what was observed* and *how the
project responded*, and cross-reference the other two rather than
repeating their content.

---

## 1. GitHub Self-Approval Limitation

- **Date:** 2026-08-05 ("Iteration 3 Batch 1").
- **Observation:** GitHub does not allow an account to approve a Pull
  Request it authored. With Experts Marketing's single shared GitHub
  identity, the first PR opened under a "Require approvals" rule could
  not clear its merge requirement — GitHub silently downgraded
  Arthur's own review submission to a comment, never an approval, and
  the branch-protection sidebar kept reporting "at least 1 approving
  review is required" indefinitely.
- **Impact:** Every Pull Request authored under that rule was
  permanently unapprovable. The workflow deadlocked entirely — not a
  slowdown, a hard stop — until the rule was removed.
- **Resolution:** Removed the "Require approvals" branch protection
  rule; replaced it with explicit chat-based approval as the actual
  gate, with auto-merge deliberately withheld until that approval
  arrives. Full decision record: `DECISIONS.md` #5 and #6.
- **Future prevention:** If a second person ever becomes an active
  GitHub contributor, re-enable "Require approvals" with that person
  as reviewer — this restores a platform-enforced gate and removes the
  single-identity constraint that caused this incident.
- **Source:** `OPERATING_MODEL.md` §1, §5.

---

## 2. Vercel Check Timing (Observed Pattern, Not a Discrete Incident)

- **Date:** Not tied to a specific dated event in the five documents
  analyzed for this log.
- **Observation:** The only material found on this topic is standing
  operational guidance, not a record of an actual delay incident:
  `WEBSITE_CHANGE_WORKFLOW.md`'s Troubleshooting section notes Vercel
  "needs 10–30 seconds to confirm the Preview built successfully," and
  frames a wait at that stage as normal rather than a problem.
- **Impact:** None recorded — this is preventive guidance, not a
  postmortem. Flagging this honestly rather than inventing an incident
  the source documents don't describe.
- **Resolution:** N/A.
- **Future prevention:** If a real webhook/check-delay incident occurs
  in the future (for example, a Vercel check that never posts, or an
  auto-merge that hangs waiting on a check that silently failed),
  record it here with its own dated entry rather than folding it into
  this placeholder — this entry should be replaced or supplemented the
  first time that actually happens.
- **Source:** `WEBSITE_CHANGE_WORKFLOW.md` §8 (Troubleshooting).

---

## 3. Mixed CRLF/LF Line-Ending Convention

- **Date:** Identified as a standing repository characteristic;
  formally documented during the 2026-08-05 documentation
  reconciliation (`DECISIONS.md` #7).
- **Observation:** The repository has a mixed line-ending convention
  across files. `index.html` and `assets/styles.css` use CRLF;
  `privacy.html`, `services/seo.html`, and `services/geo-aeo.html` use
  LF; other files haven't been audited.
- **Impact:** Editing a file with the wrong line-ending convention
  turns a small, intended change into a full-file diff, making the
  Pull Request effectively unreviewable — a real, recurring risk, not
  a one-time gotcha.
- **Resolution:** Standing rule added to the GitHub Workflow section
  of `OPERATING_MODEL.md`: check line endings (e.g. with `cat -A` or
  equivalent) before editing any file, every time, not just the first
  time a file is touched.
- **Future prevention:** The check-every-time rule is the current
  mitigation. If this keeps recurring in practice, normalizing the
  whole repository to one convention and adding a `.gitattributes`
  file would remove the risk structurally instead of relying on a
  manual check — worth considering if it becomes a repeated source of
  noisy diffs, though this specific remedy is not itself documented in
  the source material and is noted here as a reasonable next step, not
  a decision already made.
- **Source:** `OPERATING_MODEL.md` §3; `WEBSITE_CHANGE_WORKFLOW.md`
  "What Happens Behind the Scenes."

---

## 4. Browser Automation Limitations

- **Date:** Ongoing; most concretely documented 2026-08-05 in
  `CLAUDE_CODE_MIGRATION_PLAN.md`, reflecting the validated state of
  the workflow at that point.
- **Observation:** Claude in Chrome browser automation is slow
  (roughly 1.5–3+ minutes for a simple one-file change: navigate,
  click, wait, screenshot, repeat) and fragile — coupled to GitHub's
  current page layout, and has already required adjustment during
  earlier phases of this project. It also depends on the Chrome
  extension staying connected, and Claude's Cowork sandbox holds no
  persistent GitHub push credentials at all, meaning every GitHub-side
  write action routes through either the browser or a human's own
  hands.
- **Impact:** Materially slower and less reliable execution than a
  local CLI would be, and a source of ongoing maintenance risk
  (adjustments needed whenever GitHub changes its UI).
- **Resolution:** Approved migration to Claude Code running locally
  with real `git`/`gh` CLI access (`DECISIONS.md` #8) — an estimated
  ~10–15 seconds per simple change, using stable API/CLI contracts
  instead of DOM-dependent clicking.
- **Future prevention:** Cowork and browser automation are retained as
  an explicit, documented fallback for when the local machine is
  unavailable — not deleted or treated as obsolete once Claude Code is
  in place.
- **Source:** `CLAUDE_CODE_MIGRATION_PLAN.md` §2, §8, §10.

---

## 5. Preview-First Deployment Validation

- **Date:** Established as a core rule from the outset (Goal #1 of the
  operating model); reaffirmed by an explicit end-to-end validation
  test referenced in `ARTHUR_QUICK_REFERENCE.md` and
  `WEBSITE_CHANGE_WORKFLOW.md`.
- **Observation:** Every change gets a Preview Deployment before
  anything else happens, and approval is required to be based on
  actually opening and looking at the Preview — never a description
  alone. `WEBSITE_CHANGE_WORKFLOW.md` states this as a "golden rule."
  The full discovery → branch → PR → Preview → reject-cleanup path has
  been tested end-to-end, including a deliberate rejection test that
  confirmed PR-close, branch-delete, and production-unchanged all
  worked correctly.
- **Impact:** This is the primary safety mechanism protecting visual
  and content correctness, distinct from — and prior to — the
  approval-gate mechanism covered in Lesson 1. It has held up under
  direct testing, not just as a stated policy.
- **Resolution:** Not applicable — this is a standing practice that
  has worked as intended, not an incident requiring a fix.
- **Future prevention:** Both Arthur-facing documents explicitly
  instruct never approving without opening the Preview link first, and
  list it as a checklist item under "Before You Approve." The rejection
  path has been tested; the chat-gated auto-merge sequence specifically
  has not yet had its own dedicated end-to-end test (see Lesson 1 and
  `DECISIONS.md` #6) — worth running once, on a trivial change, to
  close that gap.
- **Source:** `OPERATING_MODEL.md` §0, §4; `WEBSITE_CHANGE_WORKFLOW.md`
  §4, "Validated Workflow"; `ARTHUR_QUICK_REFERENCE.md`.

---

## 6. Repository Source-of-Truth Lessons

- **Date:** 2026-08-05 (documentation reconciliation).
- **Observation:** Two independently-developed versions of the same
  three documentation filenames existed at the same time — one merged
  to GitHub `main` via PR #4, one developed locally — because parallel
  work happened without either side being aware of the other's changes
  landing on GitHub.
- **Impact:** Created a real risk of treating an incomplete or stale
  copy as authoritative in either direction — GitHub's copy could have
  been read as canonical while missing substantial locally-developed
  content, or the reverse.
- **Resolution:** A deliberate reconciliation process: a rigorous
  side-by-side comparison (word counts, structural differences, unique
  content per side) before any merge decision, a considered "local is
  the foundation" call, a specific five-item merge list, and execution
  through the normal branch → Pull Request → review cycle rather than
  a silent direct commit — documented in
  `DOCUMENTATION_RECONCILIATION_PLAN.md` and recorded in
  `DECISIONS.md` #7.
- **Future prevention:** "GitHub is the source of truth" has to be
  actively maintained, not just declared — the lesson here is
  specifically that documentation, not just code, can silently fork if
  local edits sit unpushed for an extended period. As the Claude Code
  migration (`DECISIONS.md` #8) moves more work to local editing, any
  local drafting should be pushed to GitHub promptly rather than left
  to accumulate, to avoid a repeat of this same fork.
- **Source:** `OPERATING_MODEL.md` §10 Change Log (reconciliation
  entry); `DOCUMENTATION_RECONCILIATION_PLAN.md`.
