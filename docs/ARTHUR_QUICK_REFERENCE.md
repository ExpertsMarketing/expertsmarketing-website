# Arthur's Quick Reference — Website Changes

*Keep this open while working. This document is self-contained — everything
needed to request, review, and ship a website change is on this page.
(`WEBSITE_CHANGE_WORKFLOW.md` has extra background detail, but reading it
is not required.)*

**Arthur's responsibilities — nothing more:**
- Request the change
- Review the Preview
- Explicitly approve or reject **in chat**

**Arthur never manages:** branches · commits · pushes · Pull Requests ·
merge actions · GitHub maintenance · deployment operations — and no
longer approves Pull Requests on GitHub either. Claude handles all of
it, including verifying it's working in the right GitHub organization,
repository, and Vercel project before touching anything.

```
 1. ASK          2. PREVIEW         3. APPROVE        4. LIVE
 Claude    →     Click link   →     Say "approved" →  Happens on
 (chat)          Look at it         in chat (your      its own (~1 min)
                                     last step)
```

**What changed:** GitHub's "Require approvals" rule has been turned
off — Experts Marketing runs on a single shared GitHub identity, and
GitHub won't let an account approve its own Pull Request, so that rule
would have deadlocked every change. Your approval now happens as a
plain message in this chat instead of a click on GitHub — and it's the
real gate: Claude won't turn on auto-merge until you say so.

---

## Starting a New Website Change Project

**For every significant website change, open a NEW chat.** Each change
gets its own dedicated chat — this keeps things clean and focused, the
same way you'd start a new email thread for a new topic rather than
reusing an old one. You never need to go back and reuse a previous
website-change chat; each one is self-contained and disposable once the
change is live.

```
 Step 1          Step 2                Step 3            Step 4
 Open a    →     Paste the       →     Describe    →     Continue in
 new chat        Context Prompt        the change        that chat until
                                                           deployed
```

**Step 1 — Open a new chat.** (The same place you normally talk to
Claude for this project — Cowork.)

**Step 2 — Paste the Website Context Prompt** (below) as your first
message. **Optional** if Claude already clearly knows the Experts
Marketing workflow (e.g. a project you've used before). Required in a
brand-new or unfamiliar chat. When in doubt, paste it.

**Step 3 — Describe the requested change**, in plain English.

**Step 4 — Continue the work inside that same chat** — reviewing the
Preview, requesting tweaks, approving, and confirming it's live — until
the change is fully completed and deployed. Then the chat is done; the
next change starts a fresh one.

### Website Context Prompt

Before requesting any website change, copy and paste the following
prompt as the **first message** of the new chat:

```
You are the website change assistant for Experts Marketing.

Before starting any website change:
- Locate and verify the Experts Marketing GitHub organization.
- Locate and verify the expertsmarketing-website repository.
- Locate and verify the Vercel project serving www.expertsmarketing.com.
- Confirm you have the access needed to branch, open a Pull Request,
  and enable auto-merge.
- If any access is missing, explain exactly what's missing and what's
  needed before continuing — do not guess, and do not proceed without it.

Once verified, this is the standing context:
- www.expertsmarketing.com is live on Vercel.
- GitHub is the source of truth.
- Production deployments happen from main only.
- Branch protection is enabled.
- Pull Requests are required.
- Vercel deployment checks are required before merge, and the branch
  must be up to date with main.
- GitHub's "Require approvals" rule is OFF. Experts Marketing currently
  operates with a single GitHub identity, and GitHub does not allow an
  account to approve its own Pull Request — so a GitHub-enforced review
  requirement would deadlock every change. Arthur's approval happens in
  this chat instead, explicitly, in words. That chat message is the
  real approval gate now — treat it with the same weight a required
  GitHub review used to have.
- Squash merge is the only allowed merge strategy.
- Auto-merge is available repo-wide, but must NOT be enabled the
  moment a Pull Request opens. Enable it only after Arthur explicitly
  approves in this chat — never before. Enabling it early would let
  GitHub merge the instant the Vercel check passes, with no wait for
  Arthur's review at all.
- Merged branches are automatically deleted.

Workflow:
Request
↓
Repository Verification
↓
Branch
↓
Commit
↓
Push
↓
Pull Request (auto-merge NOT enabled yet)
↓
Vercel Preview
↓
Arthur Review (in chat)
↓
Explicit Approve or Reject (in chat — this is the real approval gate)
↓
If Approved:
  Auto-Merge Enabled → GitHub Auto-Merge → Vercel Production Deploy →
  Deployment Confirmed → Live Site Verified
If Rejected:
  Close PR → Delete Branch → Production Unchanged

Your responsibilities:
1. Verify the GitHub organization, repository, and Vercel project —
   and confirm access — before doing anything else.
2. Understand the requested website change.
3. Explain the proposed implementation.
4. Create a dedicated branch.
5. Implement the change.
6. Commit and push the branch.
7. Open a Pull Request. Do NOT enable auto-merge yet.
8. Generate a Vercel Preview deployment and provide the Preview URL.
9. Wait for Arthur's explicit approval or rejection in this chat — a
   Preview link alone is not approval, and silence is not approval.
10. If approved: enable auto-merge, verify all required checks pass,
    allow GitHub Auto-Merge to complete, verify the production
    deployment, and verify the live site — then report back that it's
    live.
11. If rejected: close the Pull Request, delete the branch, and
    confirm production is unchanged.
12. Never merge or enable auto-merge before receiving explicit
    approval in chat.
13. Never ask Arthur to approve a Pull Request on GitHub — approval
    happens here, in chat, only.
14. Never bypass the workflow.
15. Never make direct production changes.

Arthur's responsibilities are limited to exactly three things: request
the change, review the Preview, explicitly approve or reject in chat.
Arthur is not expected to: approve Pull Requests on GitHub, manage
branches, manage commits, manage Pull Requests, manage merge actions,
or manage deployments — handle all of that yourself.

Assume this chat is dedicated to website changes only.
If a request concerns GitHub settings, Vercel settings, DNS, Airtable,
email infrastructure, domains, repository administration, or platform
configuration, instruct the user to use the Infrastructure/Admin chat
instead.
Always follow the Experts Marketing website workflow.
```

**Note:** when in doubt, paste it — costs nothing, and it makes sure
Claude re-verifies the repository and access instead of assuming, and
knows approval now happens in chat rather than on GitHub.

**Example — New Homepage Copy**
1. Open new chat
2. Paste Website Context Prompt
3. Write: *"Update the homepage headline to..."*

**Example — New Case Study**
1. Open new chat
2. Paste Website Context Prompt
3. Write: *"Add a new case study for..."*

Once you're inside the chat (Step 4 above), the four numbered sections
below are exactly what that step looks like in practice, from request to
live site.

### Full Workflow

```
Arthur
  ↓
Request
  ↓
Repository Verification
  (GitHub org, repository, Vercel project, and access — confirmed
   automatically before anything else happens)
  ↓
Branch → Commit → Push
  ↓
Pull Request  (auto-merge NOT enabled yet)
  ↓
Vercel Preview
  ↓
Arthur Review
  ↓
Explicit Approve or Reject (in chat)

If Approved:                       If Rejected:
  Auto-Merge Enabled                 Close PR
    ↓                                  ↓
  GitHub Auto-Merge                  Delete Branch
    ↓                                  ↓
  Vercel Production Deploy           Production Unchanged
    ↓
  Deployment + Live Site Verified
```

### ✅ Validated Workflow

The repository-discovery → branch → PR → Preview → reject-cleanup path
has been tested end-to-end (repo/org/project found automatically, PR
created, Preview reviewed, rejection tested — PR closed, branch
deleted, production untouched). That test ran under the previous
required-GitHub-review model, before "Require approvals" was turned
off and approval moved into chat. The sequence above — auto-merge held
back until an explicit chat approval — reflects that change but hasn't
had its own dedicated end-to-end test yet; worth a first real approval
run to confirm auto-merge only fires after you say so.

---

### 1. Request a Change

Just type it in plain English, no technical wording needed:

> "Update the homepage headline to say ___"
> "Add a case study for [client]"
> "Fix the address on the About page"

Claude handles everything else. Wait for a **Preview URL**.

**💡 Tip:** more detail = better first draft. *"Update the headline"*
works, but *"...to focus on our new AI visibility service, under 10
words"* gets it right faster — fewer rounds of tweaks.

---

### 2. Review the Preview

- Click the link Claude sends you (ends in `.vercel.app`).
- Look at the actual page — not just Claude's description.
- Not right yet? Just say what to change — same link updates automatically.
- ✅ Looks good → move to Step 3.

**Never approve without opening the Preview first.**

**✅ Before You Approve — quick checklist:**
- [ ] Open the Preview URL
- [ ] Test on desktop
- [ ] Test on mobile
- [ ] Check unrelated content wasn't accidentally affected
- [ ] Check links still work
- [ ] Confirm text accuracy (names, numbers, spelling)

---

### 3. Approve or Reject — In Chat

No GitHub, no login, no button. Just reply in chat:

> "Approved"
> "Reject this — the numbers are wrong"

That's the whole step. GitHub's own approval requirement had to be
turned off (single shared GitHub identity — GitHub won't let an
account approve its own Pull Request), so your word in chat is now the
actual gate: Claude will not turn on auto-merge until you've explicitly
said yes.

**This is your last step.** No merge button, no GitHub click, no login — Section 4 happens on its own once you've approved.

**Not right?** Just tell Claude in chat that it's a no, and why if you want. Claude closes the Pull Request, deletes the branch, and production stays exactly as it was — nothing further for you to clean up.

---

### 4. Go Live — Automatic, No Extra Click

Nothing to click here. The moment you approve in chat, Claude turns on
auto-merge; GitHub merges as soon as the Vercel check passes (usually
already true), and Vercel deploys.

- No merge button, no second approval, no GitHub visit.
- Live on **www.expertsmarketing.com** in about a minute.
- Claude verifies the checks passed, confirms the deployment, and
  checks the live site before telling you it's done.

---

### 🚨 Emergency Process

**Something's broken right now?**

1. Tell Claude immediately — say it's urgent.
2. Caused by a recent change → **instant rollback**, seconds, no new approval needed.
3. Something else broken → Claude still uses Preview + approval, just fast-tracked.
4. Not sure / unavailable → loop in **Christian**.

**Nothing is ever permanently lost — any past version can be restored.**

---

*Do not:* edit Plesk directly (retired) · approve without previewing · touch DNS/domain settings without asking Claude first.
