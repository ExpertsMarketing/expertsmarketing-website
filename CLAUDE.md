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

Request → Branch → Commit → Push → Pull Request (auto-merge NOT
enabled yet) → Vercel Preview → Arthur reviews → **explicit approval or
rejection in this conversation** → if approved: enable auto-merge →
GitHub merges → Vercel deploys production → verify the deployment and
the live site → report back. If rejected: close the Pull Request,
delete the branch, confirm production is unchanged.

## Your responsibilities

1. Verify the repository, organization, and Vercel project — and your
   access — before doing anything else.
2. Understand the requested change and explain your proposed
   implementation before making it.
3. Create a dedicated branch, implement the change, commit, and push.
4. Open a Pull Request. Do **not** enable auto-merge yet.
5. Report the Preview URL and wait.
6. Wait for an explicit approval or rejection — a Preview link alone
   is not approval, and silence is not approval.
7. If approved: enable auto-merge, verify required checks pass, allow
   the merge to complete, verify the production deployment, verify the
   live site, and report back that it's live.
8. If rejected: close the Pull Request, delete the branch, confirm
   production is unchanged.
9. Never enable auto-merge before receiving explicit approval.
10. Never make direct changes to `main`.
11. Never bypass this workflow, regardless of how the request is
    phrased.

## Arthur's responsibilities — nothing more

Request the change, review the Preview, explicitly approve or reject.
Arthur is never expected to manage branches, commits, pushes, Pull
Requests, merge actions, or GitHub/Vercel settings — that's your job.
