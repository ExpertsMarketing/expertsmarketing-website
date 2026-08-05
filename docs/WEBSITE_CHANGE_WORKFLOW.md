# Website Change Workflow

Step-by-step procedure for shipping a change to www.expertsmarketing.com.
See `OPERATING_MODEL.md` for the rationale behind this process. See
`ARTHUR_QUICK_REFERENCE.md` for a one-page cheat sheet.

## Prerequisites

- Repo: `ExpertsMarketing/expertsmarketing-website`
- No build step — plain HTML/CSS. `assets/styles.css` is the shared
  stylesheet; `services/` holds service pages; `docs/` holds internal
  planning docs.
- **Check line endings before editing a file.** The repo has a mixed
  convention: `index.html` and `assets/styles.css` use CRLF;
  `privacy.html`, `services/seo.html`, and `services/geo-aeo.html` use
  LF. Other files haven't been audited — check with `cat -A` (or
  equivalent) before assuming. Editing with the wrong line ending
  turns a small change into a full-file diff and makes the PR
  unreviewable.

## Steps

1. **Verify repo and project.** Confirm you're targeting
   `ExpertsMarketing/expertsmarketing-website` and Vercel project
   `expertsmarketing-website`. Confirm current branch protection on
   `main` matches §3 of `OPERATING_MODEL.md` before relying on the
   auto-merge behavior below.

2. **Create a branch off `main`.** One branch per logical batch of
   changes — don't bundle unrelated fixes into one PR.

3. **Make the edits.** Match the existing line-ending convention
   per-file (see Prerequisites). Keep diffs targeted: change only what
   the task calls for.

4. **Open a pull request** from the branch into `main`. Enable
   **auto-merge (squash)** on the PR immediately — it won't fire until
   the Vercel check passes, so enabling it early doesn't skip review.

5. **Wait for the Vercel Preview.** Vercel comments on the PR with a
   Preview URL once the deployment is ready.

6. **Review the Preview.** Open the Preview URL and check the actual
   rendered change — this is the approval gate now that GitHub-native
   review approval is off (see `OPERATING_MODEL.md` §2). If anything
   looks wrong, fix it on the same branch (auto-merge won't fire until
   Vercel is green) or close the PR.

7. **Approve or reject, in chat.** State explicitly whether the
   Preview is approved. Once approved, auto-merge proceeds
   automatically — no separate merge action needed.

8. **Confirm the merge.** Check that the PR shows "Merged," the branch
   was auto-deleted, and the merge commit landed on `main`.

9. **Confirm the production deployment.** Check Vercel for a new
   deployment with `target: production` matching the merge commit SHA,
   state `READY`.

10. **Verify live.** Fetch the changed page(s) on
    www.expertsmarketing.com. **Use a cache-busted request** (e.g.
    `fetch(url + '?cachebust=' + Date.now(), {cache: 'no-store'})` run
    in an actual browser context) rather than a generic fetch —
    CDN/edge caching can return stale content immediately after a
    deploy and produce a false negative.

## Notes

- No git CLI or token access is assumed. File changes are committed
  through GitHub's web upload UI (`/OWNER/REPO/upload/BRANCH/path`).
- If a PR needs more than a passing Vercel check to merge (branch
  protection changes, emergency rollback, etc.), that's outside this
  standard flow — pause and confirm with Arthur before proceeding.
