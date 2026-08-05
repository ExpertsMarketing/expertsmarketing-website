# Arthur's Quick Reference — Website Changes

One-page cheat sheet. Full detail in `OPERATING_MODEL.md` and
`WEBSITE_CHANGE_WORKFLOW.md`.

## The flow

**Request → Preview → Approve or Reject → Auto-Merge → Production**

You review the live Vercel Preview and say "approved" or "rejected" in
chat. That's the only approval step — GitHub's own "Require approvals"
review gate is off, because your account is the only active
contributor and GitHub won't let an author approve their own PR.
Everything else (PR required, Vercel check required, squash-only,
auto-merge, auto-delete branches) is still enforced.

## What to expect each time

1. You ask for a change or batch of changes.
2. A branch + PR gets created, auto-merge turned on.
3. Vercel posts a Preview link on the PR.
4. You get the Preview link — go look at it.
5. You say "approved" (or flag what's wrong).
6. It auto-merges, deploys to production, and gets verified live.
7. You get a confirmation with what changed and where.

## Things to know

- **No manual "Approve" click needed on GitHub.** Your approval
  happens by reviewing the Preview and saying so in chat.
- **One branch per batch.** Unrelated fixes don't get bundled together.
- **Mixed line endings exist in this repo** — `index.html` and
  `assets/styles.css` use CRLF; `privacy.html`,
  `services/seo.html`, `services/geo-aeo.html` use LF. This is handled
  automatically during edits; flagged here only so it's not a mystery
  if it comes up.
- **If a change needs anything beyond the standard flow** (e.g.
  touching branch protection, rolling back a bad deploy), that's
  called out explicitly and confirmed with you first — it doesn't
  happen silently.

## If something looks wrong on a Preview

Say so before approving. The branch stays open, the fix goes on the
same branch, and a new Preview gets generated — nothing merges until
you're satisfied.
