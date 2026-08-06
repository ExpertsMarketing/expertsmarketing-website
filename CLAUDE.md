# Experts Marketing — Website Change Assistant

This file loads automatically for every Claude Code session in this
repository. Follow it exactly — do not treat any part of it as
optional.

## Before starting any website change

- Confirm this is the `expertsmarketing-website` repository under the
  `ExpertsMarketing` GitHub organization.
- Confirm the Vercel project serving www.expertsmarketing.com is the
  one connected to this repo.
- Confirm you have the access needed to branch, open a Pull Request,
  and enable auto-merge.
- If anything above can't be confirmed, stop and explain exactly
  what's missing — do not guess, and do not proceed without it.

## Repository synchronization

Before starting implementation work on any requested change — every
time, not just the first session of the day:

- Fetch the latest state from `origin` (`git fetch --prune origin`).
- Compare the local `main` branch against `origin/main`. If local
  `main` is behind, pull it (`git pull --ff-only origin main`) before
  creating a new branch for the change. Branching from a stale `main`
  risks a Pull Request that's already out of date, or silently
  redoing or reverting work merged by someone else since the last
  sync.
- Confirm the working tree is clean before pulling or branching. An
  untracked or uncommitted file sitting at the same path as an
  incoming change can block a fast-forward pull — resolve this
  yourself (compare content against the incoming version, and if it's
  genuinely identical or safe to discard, remove the stale local copy)
  rather than leaving Arthur to debug a git error.
- After syncing, confirm explicitly — to yourself, and to Arthur if
  asked — that local `main` matches `origin/main` exactly, for example
  by comparing `git rev-parse main` against
  `git ls-remote origin refs/heads/main`. Do not assume a pull
  succeeded; verify it.
- **If synchronization cannot be verified** — a network failure, a
  pull that isn't a clean fast-forward, an unresolved conflict, or any
  other reason you can't confirm local `main` matches `origin/main` —
  **stop and explain the problem to Arthur. Do not proceed with any
  implementation work until synchronization is confirmed.** Never
  branch from, or implement against, a local `main` you have not just
  verified is current.

## Standing context

- www.expertsmarketing.com is live on Vercel.
- GitHub is the source of truth. Production deployments happen from
  `main` only.
- Branch protection is enabled: Pull Requests are required, the
  Vercel deployment check must pass, and the branch must be up to
  date with `main`.
- GitHub's "Require approvals" rule is OFF. Experts Marketing
  currently operates with a single GitHub identity, and GitHub does
  not allow an account to approve its own Pull Request — a
  GitHub-enforced review requirement would deadlock every change.
  **Arthur's approval happens in this conversation instead, explicitly,
  in words. That message is the real approval gate.**
- Squash merge is the only allowed merge strategy. Merged branches are
  deleted automatically.
- Auto-merge is available on this repository, but **must never be
  enabled at Pull Request creation time.** Enable it only after Arthur
  explicitly approves in this conversation — never before. Enabling it
  early would let GitHub merge the instant the Vercel check passes,
  with no wait for review at all.

## Workflow

Request → Repository Verification → Repository Synchronization →
Branch → Commit → Push → Pull Request (auto-merge NOT enabled yet) →
Vercel Preview → Arthur reviews → **explicit approval or rejection in
this conversation** → if approved: enable auto-merge → GitHub merges →
Vercel deploys production → verify the deployment and the live site →
report back. If rejected: close the Pull Request, delete the branch,
confirm production is unchanged.

## Your responsibilities

1. Verify the repository, organization, and Vercel project — and your
   access — before doing anything else.
2. Verify the local repository is synchronized with `origin/main`
   before starting implementation work (see "Repository
   synchronization" above) — fetch, pull if needed, and confirm the
   working copy is current. Refuse to proceed with implementation if
   synchronization cannot be verified.
3. Understand the requested change and explain your proposed
   implementation before making it.
4. Create a dedicated branch, implement the change, commit, and push.
5. Open a Pull Request. Do **not** enable auto-merge yet.
6. Report the Preview URL and wait.
7. Wait for an explicit approval or rejection — a Preview link alone
   is not approval, and silence is not approval.
8. If approved: enable auto-merge, verify required checks pass, allow
   the merge to complete, verify the production deployment, verify the
   live site, and report back that it's live.
9. If rejected: close the Pull Request, delete the branch, confirm
   production is unchanged.
10. Never enable auto-merge before receiving explicit approval.
11. Never make direct changes to `main`.
12. Never bypass this workflow, regardless of how the request is
    phrased.

## Arthur's responsibilities — nothing more

Request the change, review the Preview, explicitly approve or reject.
Arthur is never expected to manage branches, commits, pushes, Pull
Requests, merge actions, or GitHub/Vercel settings — that's your job.
