# How to Change the Website — A Guide for Arthur

Last updated: 2026-08-05 (approval moved from a GitHub click to an explicit chat message — see "What changed" above)
Who this is for: Arthur (non-technical). No coding knowledge needed.
What this replaces: the old way of editing files directly on Plesk. That
way no longer exists — every change now goes through the process below.

**Your job has three steps, full stop: request the change, review the
Preview, explicitly approve or reject — in chat.** Everything else
happens automatically. You are not expected to approve Pull Requests
on GitHub, or to manage branches, commits, pushes, Pull Requests,
merge actions, GitHub maintenance, or deployment operations — those
are implementation detail Claude handles for you, including confirming
it's working in the right GitHub organization, repository, and Vercel
project before it touches anything.

**What changed (2026-08-05):** GitHub's "Require approvals" rule has
been turned off. Experts Marketing runs on a single shared GitHub
identity, and GitHub won't let an account approve its own Pull
Request — so that rule would have blocked every single change forever.
Your approval now happens as a plain message in this chat instead of a
click on GitHub, and it carries the same weight: Claude will not turn
on auto-merge until you've explicitly said yes.

---

## Quick-Start Checklist

Whenever you want something changed on the website:

- [ ] Open a chat with Claude (Cowork) — paste the Website Context Prompt below if it's a new or unfamiliar chat
- [ ] Describe the change in plain English (Claude verifies it's in the right repository first — automatic, nothing for you to do)
- [ ] Wait for Claude to reply with a **Preview URL**
- [ ] Click the Preview URL and look at the change on the actual page
- [ ] If it looks right, reply in chat: "approved" — if not, tell Claude what to fix, or say it's a reject
- [ ] That's your last step. No GitHub, no login. Merging, deploying, and cleanup all happen automatically.
- [ ] Check www.expertsmarketing.com in a minute or two to confirm it's live

That's it — three things you actually do: request, review, approve/reject
in chat. You never touch code, never touch Plesk, never touch DNS, never
log into GitHub, and you never need to know what a branch or a Pull
Request is.

---

## Starting a New Website Change Chat

Paste the **Website Context Prompt** below as your first message in a
new or unfamiliar chat. It tells Claude to verify it's working with the
right GitHub organization, repository, and Vercel project — and to
tell you plainly if any access is missing — before it changes anything.
It also tells Claude that your approval now happens as a message in
chat, not a click on GitHub. Optional if you're continuing in a chat
Claude has already used for this project; when in doubt, paste it.

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

---

## Contents

1. [Where to Go, What to Open](#1-where-to-go-what-to-open)
2. [How to Ask Claude for a Change](#2-how-to-ask-claude-for-a-change)
3. [The Three Examples, Step by Step](#3-the-three-examples-step-by-step)
4. [How the Preview URL Works](#4-how-the-preview-url-works)
5. [How Approval Works](#5-how-approval-works)
6. [How It Goes Live (Production Deployment)](#6-how-it-goes-live-production-deployment)
7. [What Happens Behind the Scenes](#what-happens-behind-the-scenes)
8. [Validated Workflow](#validated-workflow)
9. [Do and Don't](#7-do-and-dont)
10. [Troubleshooting](#8-troubleshooting)
11. [Emergency Fixes](#9-emergency-fixes)
12. [How to Roll Back a Change](#10-how-to-roll-back-a-change)
13. [When Christian Needs to Be Involved](#11-when-christian-needs-to-be-involved)
14. [A Few Things Worth Remembering](#12-a-few-things-worth-remembering)

---

## 1. Where to Go, What to Open

You only need two things open:

1. **A chat with Claude** — this is where you ask for the change, in plain
   English, review it, and say approved or reject — the same way you'd
   ask a colleague and then give them a thumbs up.
2. **A web browser** — just to look at the Preview link Claude sends
   you. You no longer need it to approve anything on GitHub.

You do not need: Plesk, an FTP program, a code editor, or any developer
tool. You do not need to touch DNS or the domain settings ever again for
routine content changes.

Before Claude does anything else, it confirms it's working with the
right GitHub organization, the right repository, and the right Vercel
project, and that it has the access it needs. This happens
automatically and you won't normally see it — if something's actually
missing, Claude will tell you plainly what's needed rather than
guessing or proceeding anyway.

*[Screenshot placeholder: Claude chat window with an example request typed in]*

---

## 2. How to Ask Claude for a Change

Just describe what you want, like you're leaving instructions for a
person. You don't need special wording, technical terms, or exact page
names — Claude will figure out which file(s) need to change and ask you
if anything is ambiguous.

Good examples of how to ask:

> "Can you update the homepage headline to say something about our new
> AI visibility service?"

> "We just finished a project for a client called Wiki Thinkers — can
> you add it as a new case study?"

> "The About Us page still mentions our old office in a way that's
> outdated, can you fix that?"

If your request is small (a typo, a wrong phone number, a broken link),
just say so — it still goes through the same safe process, but it'll be
quick.

*[Screenshot placeholder: example chat message from Arthur asking for a homepage headline change]*

---

## 3. The Three Examples, Step by Step

### Example 1: "Update the homepage headline"

1. You say: *"Change the homepage headline to 'Win the searches that
   drive revenue — on Google and AI, guaranteed.'"*
2. Claude edits the homepage file on a separate, temporary branch —
   nothing on the live site changes yet.
3. Claude opens a Pull Request (a formal "here's the proposed change")
   and Vercel automatically builds a **Preview** version of the site
   with your new headline.
4. Claude sends you a Preview link, e.g.
   `https://expertsmarketing-website-git-change-headline-experts-marketing.vercel.app`
5. You open it, see the new headline live on a real page (not a
   description — the actual page), and decide if it looks right.
6. If yes, you reply in chat: "approved" (Section 5 explains exactly how).
7. Within a minute or two, www.expertsmarketing.com shows the new
   headline.

### Example 2: "Add a new case study"

1. You say: *"Add a new case study for [Client Name] — here's what we
   achieved for them: [details]."*
2. Claude adds a new entry to the results/case-studies page, following
   the same layout as the existing ones.
3. Same Preview → review → approve → live process as above.
4. Because this adds new content rather than editing existing text,
   it's worth double-checking on the Preview: correct client name
   spelling, correct numbers/results, and that the new entry appears
   in the right place on the page.

### Example 3: "Update the About Us page"

1. You say what needs to change — new team info, updated company
   description, corrected address, whatever it is.
2. Same process. About Us tends to have more paragraphs than the
   homepage, so it's worth reading the whole Preview page top to
   bottom, not just the part you asked to change, in case anything
   shifted visually.

*[Screenshot placeholder: side-by-side of old vs. new homepage headline on the Preview URL]*

---

## 4. How the Preview URL Works

Every single change — big or small — gets its own **Preview URL**
before it ever touches the real website. Think of it as a private,
fully working copy of the site with just your change applied, that
only people with the link can see.

- The Preview looks and behaves exactly like the real site — you can
  click around, check it on your phone, send the link to Christian or
  anyone else for a second opinion.
- Nothing about the Preview is visible to your actual visitors or
  affects www.expertsmarketing.com in any way.
- Preview links stay around even after a change goes live, so if you
  ever want to double check "what did this look like before we
  approved it," Claude can find the old Preview link again.

**Golden rule: never approve a change based on Claude's description
alone. Always click the Preview link and look at it yourself first.**

*[Screenshot placeholder: a Preview URL open in a browser tab, clearly showing it's a vercel.app address, not www.expertsmarketing.com]*

### If the Preview isn't quite right

This is completely normal — first drafts often need a tweak. Just tell
Claude what to adjust, the same way you'd give feedback on a draft
email: *"Can you make the headline a bit shorter"* or *"the case study
number should be 40%, not 35%."*

Claude updates the same branch, and the **same Preview link
automatically refreshes** with the new version — you don't get a new
link to track, and nothing needs to be re-approved from scratch until
you're happy with it. Only approve once the Preview looks exactly
right.

---

## 5. How Approval Works

This is the one manual step that always requires you (or Christian).
Claude cannot approve its own changes — this is deliberate, so nothing
reaches the live site without a person actually looking at it.

**Approval happens in chat now, not on GitHub.** GitHub's own "require
a review" setting had to be turned off: Experts Marketing runs on a
single shared GitHub account, and GitHub will not let an account
approve its own Pull Request — with that rule on, every single change
would have been stuck forever, waiting on an approval nobody could
give. So the review step moved from a GitHub click to a plain message
in this chat. It carries exactly the same weight — Claude will not
enable auto-merge until you've explicitly approved.

Steps:

1. Open the Preview link and look at it (Section 4).
2. Reply in chat, in your own words — for example:
   > "Approved"
   > "That looks good, ship it"
   > "Reject this — the phone number is wrong"
3. Claude only proceeds toward production after a message that's
   clearly a yes. Opening the Preview link is not approval by itself,
   and staying quiet is not approval either — say so explicitly.
4. Once you approve, there's nothing else to click — no GitHub, no
   login, no second confirmation. See Section 6.

You can do this from a phone just as easily as a computer, since it's
just a chat message — no GitHub screen to load.

### If You Reject

Telling Claude it's a no is a completely normal outcome, not a
failure. When you reject a change:

- Claude closes the Pull Request.
- The branch is deleted automatically.
- Production stays exactly as it was — nothing was ever at risk of
  reaching the live site, since auto-merge is never turned on until
  you approve.

There's no cleanup for you to do either way. Just tell Claude what to
try differently, or drop it entirely.

---

## 6. How It Goes Live (Production Deployment)

There is no separate "make it live" button — and now, no separate
GitHub visit either. The moment you approve in chat:

1. Claude turns on GitHub's auto-merge for that Pull Request (it was
   deliberately left off until this exact moment — see Section 5).
2. GitHub merges the change as soon as the Vercel check passes, which
   is usually already true (it's the same check that built your
   Preview).
3. Vercel automatically detects the merge and rebuilds the live site —
   no separate "deploy" step, no waiting for a person to trigger
   anything.
4. Within roughly a minute, www.expertsmarketing.com shows the change.
5. Claude verifies the checks passed, confirms the production
   deployment succeeded, and checks the live site itself — using a
   technique that avoids being fooled by cached/stale content, not
   just a quick look — and reports back that it's done.

You do not need to do anything else after approving. No FTP upload, no
"publish" button, no "merge" button, no server restart, no GitHub
login — that entire category of work no longer exists for this site.

---

## What Happens Behind the Scenes

You don't need to understand this to use the process — but if you're
curious, here's the full path a single request takes:

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
Explicit Approve or Reject (in chat — the real approval gate)

If Approved:                       If Rejected:
  Auto-Merge Enabled                 Close PR
    ↓                                  ↓
  GitHub Auto-Merge                  Delete Branch
    ↓                                  ↓
  Vercel Production Deploy           Production Unchanged
    ↓
  Deployment + Live Site Verified
```

Every box in that chain is a safety checkpoint. Auto-merge is
deliberately left off until you approve in chat — that ordering is
what makes your chat message a real gate now that GitHub's own review
requirement is off (Section 5). Nothing skips a step — not even for
Claude, not even for a one-word typo fix — and nothing needs a GitHub
visit from Arthur either way.

**A technical detail, for completeness, not something you need to act
on:** this repository has a mixed line-ending convention across files
(some use CRLF, some use LF). Claude checks and matches the existing
convention before editing any file — flagged here only so it's not a
mystery if you ever see it mentioned; getting it wrong would bloat a
PR's diff, not break anything on the live site. Full detail in
`OPERATING_MODEL.md` §3, for anyone technical who wants it.

---

## Validated Workflow

The repository-discovery → branch → PR → Preview → reject-cleanup path
has been tested end-to-end:

- A new chat correctly found the Experts Marketing GitHub organization,
  the `expertsmarketing-website` repository, and the right Vercel
  project on its own, with no manual setup from Arthur.
- A branch and a commit were created, and a Pull Request was opened.
- A Vercel Preview was generated and reviewed.
- The change was rejected on purpose, as a deliberate test of the
  rejection path.
- The Pull Request was closed and the branch was deleted
  automatically.
- Production was confirmed unchanged throughout the test.

**Note:** that test ran under the previous model, where GitHub's
required-review setting was the safety gate and auto-merge was enabled
as soon as the PR opened. Since then, "Require approvals" has been
turned off (single shared GitHub identity — self-approval isn't
possible) and approval moved into chat, with auto-merge now
deliberately held back until that chat approval arrives (Section 5,
and the diagram above). That specific reordering hasn't had its own
dedicated end-to-end test yet — worth doing once, on a trivial change,
to confirm auto-merge really does wait for your word before it fires.

---

## 7. Do and Don't

**Do:**
- Describe changes in plain English — no need to mention file names,
  HTML, or anything technical.
- Always open the Preview link and actually look at the change before
  approving.
- Ask Claude questions if you're not sure what a change will look like
  before committing to it — you can ask for a Preview without approving
  it.
- Loop in Christian for anything you're unsure about, especially
  content involving numbers, client names, or legal/privacy text.
- Ask Claude to check something on the live site if you think
  something looks broken — that's always safe, it's just looking, not
  changing anything.

**Don't:**
- Don't ask anyone to edit files directly on Plesk — that system is
  retired for this site.
- Don't approve in chat without opening the Preview link first.
- Don't assume opening the Preview counts as approval — say "approved"
  (or similar) explicitly; Claude waits for a clear yes before doing
  anything further.
- Don't ask Claude to "just push it live" skipping Preview — the
  system is built so this isn't possible even if asked; every change
  goes through Preview and approval, no exceptions.
- Don't change DNS, domain settings, or Vercel account settings
  yourself without looping in Claude first — these are rare,
  high-risk actions, not routine content edits.
- Don't panic if a Preview looks broken — nothing is live yet at that
  point; just tell Claude what's wrong and it'll be fixed before
  anything reaches production.
- Don't feel like you need to manage branches, commits, pushes, Pull
  Requests, merges, or GitHub repository settings yourself — that's
  never your job, request/review/approve-or-reject is.

---

## 8. Troubleshooting

**"Claude says it's waiting on the Vercel check."**
This is normal — Vercel needs 10–30 seconds to confirm the Preview
built successfully. Once that check has passed and you've said
"approved," there's nothing further to wait on from your side — merge
and deploy happen automatically.

**"I said approved — is there anything else I need to click?"**
No. There's no GitHub button, no login, no separate "make it live"
step. Once you've approved in chat, Claude turns on auto-merge and
GitHub merges and deploys automatically, usually within a minute
(Section 6). If it's been longer than that, ask Claude to check the
deployment status.

**"The Preview link doesn't work / shows an error."**
Ask Claude to check the deployment status — occasionally a Preview
build fails (usually a typo in the code Claude wrote, not something
you did). Claude can see the error and fix it; a new Preview link will
follow.

**"I approved something by mistake, or want to undo a change I already
approved."**
See Section 10 (How to Roll Back a Change) — this is always possible
and usually takes seconds.

**"I don't see any change on www.expertsmarketing.com after
approving."**
Give it a minute or two. If it's been longer than 5 minutes, ask
Claude to check the deployment status directly rather than assuming
something's wrong — browsers sometimes show a cached (old) version of
a page; try a hard refresh (Ctrl+Shift+R on Windows, Cmd+Shift+R on
Mac) before concluding anything is stuck.

**"Christian and I both need to look at something — can we both get
the Preview link?"**
Yes — Preview links can be shared with anyone; they don't require a
GitHub or Vercel login to view.

**"Do I ever need to log into GitHub?"**
No, not for the normal workflow — approving and rejecting both happen
in chat now. The repository is public, so even opening a Pull Request
link out of curiosity to see "Files changed" doesn't require a login.
GitHub access only comes up for rare, higher-risk changes (Section 11)
that go through Christian or a separate infrastructure conversation.

---

## 9. Emergency Fixes

If something is visibly broken on the live site right now (a page
won't load, the contact form is failing, something urgent), tell
Claude immediately and say it's urgent. Two things can happen, and
Claude will tell you which applies:

1. **If the problem was caused by a recent change:** the fastest fix
   is an instant rollback (Section 10) — this can take effect in
   seconds and doesn't require a new Preview/approval cycle, because
   it's reverting to a version that was already approved and live
   before.
2. **If it's a new problem** (not caused by a recent change — e.g. the
   contact form's email service is down): Claude will still go through
   branch → Preview → approval for any code fix, but will flag it as
   urgent so you can review and approve quickly rather than at your
   normal pace. Even emergencies don't skip Preview review — a broken
   fix shipped in a panic is worse than a 5-minute delay.

There is no scenario where the safest response is to bypass Preview or
approval entirely — the process is designed to be fast enough to use
even under time pressure.

---

## 10. How to Roll Back a Change

If a change that's already live turns out to be wrong, there are two
ways to undo it, and Claude will pick the right one:

**Fast rollback (seconds):** Claude (or Christian) can tell Vercel to
instantly switch back to the last known-good version of the site — no
new Preview, no new approval needed, since that older version was
already reviewed and approved when it first went live. This is the
right choice when something is visibly broken and speed matters most.

**Clean rollback (a few minutes):** Claude opens a new Pull Request
that undoes just the specific change, and it goes through the normal
Preview → approval → merge cycle like any other change. This is the
right choice when there's no urgency and you want the site's history
to stay tidy and easy to understand later.

Either way: **nothing is ever permanently lost.** Every past version of
every page is kept, and any of them can be restored.

---

## 11. When Christian Needs to Be Involved

Christian has the same ability you do to approve or reject in chat, so
day-to-day changes don't strictly require both of you. That said, loop
Christian in when:

- The change involves numbers, client results, or case study details
  he originally worked on — a second set of eyes on accuracy.
- You're not fully sure whether a change is a good idea and want a
  second opinion before it goes live.
- Something urgent comes up and you're not immediately available to
  review a Preview or approve.
- Anything touches the technical side beyond routine content — domain
  settings, DNS, redirects (`vercel.json`), or the contact
  form/Airtable/email integration. These aren't "no" — they're just
  higher-risk changes worth a second person's awareness before they
  ship.

For purely routine content updates (headline tweaks, new case studies,
copy edits), either of you approving alone is entirely normal and
expected.

---

## 12. A Few Things Worth Remembering

- **Nothing is ever truly "gone."** Every change is tracked, every past
  version is recoverable, and mistakes are always fixable — usually in
  under a minute.
- **The contact form, Airtable, and email notifications are unaffected
  by any of this.** Routine content changes to pages never touch that
  integration.
- **You will never be asked to "just trust it."** Every change is
  something you can see with your own eyes on a real, working Preview
  page before it affects a single visitor.
- **Your job is three things: request, review, approve/reject —
  explicitly, in chat.** Branches, Pull Requests, merging, GitHub
  approvals, and deployment are Claude's and GitHub's job, not yours —
  you never need to learn what those words mean, or log into GitHub,
  to use this process.

*[Screenshot placeholder: final live page on www.expertsmarketing.com showing the completed change]*

---

*This document lives at `docs/WEBSITE_CHANGE_WORKFLOW.md` in the
`expertsmarketing-website` repository. It complements
`OPERATING_MODEL.md` (the technical version of this same process, for
reference) and `GO_LIVE_PLAN.md` (the one-time migration record). As of
2026-08-05, GitHub's "Require approvals" rule is off (single shared
GitHub identity makes self-approval impossible), and approval happens
as an explicit chat message instead — Claude holds auto-merge back
until that message arrives. The repository-verification →
branch → PR → Preview → reject-cleanup path has been tested end-to-end
(see "Validated Workflow" above); the chat-gated auto-merge sequence
specifically has not yet had its own dedicated test. Explicit approval
or rejection in chat is the last manual step in the process either
way. If anything in this guide stops matching reality — a button
moves, a step changes — ask Claude to update it.*
