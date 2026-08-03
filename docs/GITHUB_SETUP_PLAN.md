# GitHub Setup Plan

Status: DRAFT — no GitHub organization or repository has been created
yet. This is a plan for review, to be executed only after approval.
Primary technical owner (GitHub account of record): adeperthuis@gmail.com
Business/project email (Experts Marketing SL): arthur@expertsmarketing.com

## 1. Organization

- Name: `ExpertsMarketing`
- Type: GitHub Organization (not a personal repo) — keeps the project
  decoupled from any one personal account, easier to hand off or add
  collaborators later without transferring ownership of a personal repo.
- Owner: adeperthuis@gmail.com — sole Owner initially. This is Arthur's
  personal technical account, designated as the primary technical owner
  for GitHub and Vercel infrastructure. arthur@expertsmarketing.com
  remains the business/project email for Experts Marketing (contact-form
  notifications, business communications) but is deliberately **not**
  the GitHub account of record.
- Billing: Free tier is sufficient (private repo, no CI minutes needed
  for a static site with no build step).

## 2. Repository

- Name: `expertsmarketing-website`
- Visibility: **Private** (decided). This avoids exposing the Google
  Apps Script URL and hCaptcha configuration in a publicly browsable git
  history — even though the same values are visible in the deployed
  page source, there's no reason to make discovery easier via GitHub
  search/indexing.
- Default branch: `main`. This is the production source of truth —
  whatever is on `main` is what Vercel deploys to production.

## 3. Branch Strategy

- `main` — protected, production-mirroring branch. Never edited
  directly.
- Feature/change branches — one per change (e.g.
  `update-about-copy`, `fix-contact-form-labels`), created for every
  edit, however small, so every change gets its own Preview URL.
- No long-lived `develop` or `staging` branch needed — Vercel's
  per-branch Preview Deployments already provide that function without
  extra branch-management overhead.

## 4. Branch Protection & Merge Strategy (on `main`)

- Require a pull request before merging (no direct pushes, including
  from Arthur — this is what guarantees every change gets a Preview URL
  and a visual review step, per the working rules of this project).
- Require the Vercel deployment check to pass (Vercel automatically
  posts a status check with the Preview URL on every PR).
- **Merge method: Squash merge only** (decided) — disable "Create a
  merge commit" and "Rebase and merge" in repo settings, leaving only
  "Squash and merge" available. This keeps `main`'s history as one clean
  commit per PR/change, and makes git revert straightforward (§7 of
  MIGRATION_PLAN.md relies on this).
- Do not require additional approving reviews initially (small team) —
  revisit if more collaborators are added beyond Arthur and Christian.

## 5. Repository Structure

Mirrors PROJECT_ARCHITECTURE.md §4:

```
expertsmarketing-website/
├── index.html, about.html, contact.html, results.html, privacy.html
├── services/*.html
├── assets/{styles.css, images/, fonts/}
├── robots.txt, sitemap.xml
├── vercel.json          # added in Phase 2 of the migration
├── docs/                # this documentation set, plus legacy-plesk.htaccess
├── .gitignore
└── README.md
```

`README.md` should be kept close to the existing one (it already
correctly describes the site as framework-free, relative-path, and
easy to preview locally) but updated to reference the new GitHub +
Vercel workflow instead of the current "drag onto Netlify drop" style
instructions.

## 6. Excluded from the Repository

Per PROJECT_ARCHITECTURE.md §4, the initial commit (and all subsequent
history) excludes: `css/`, `img/`, `plesk-stat/`, `test/`,
`updated-website.zip`, backup/`.bak` files, and root `favicon.ico`
(confirmed unused). These are Plesk-era artifacts, not part of the live
site, and should never enter version control — bringing them in "just to
be safe" would only need to be cleaned up later and adds confusion to
the source of truth.

## 7. Commit Conventions

Lightweight, not enforced by tooling initially:
- Present-tense, descriptive subject line (`Fix broken CTA links on
  contact page`, not `fixed stuff`).
- One logical change per PR where practical, so Preview review stays
  easy to reason about and squash-merge commits on `main` stay
  meaningful.
- Reference the working rule from the project instructions in PR
  descriptions when a change affects business functionality (contact
  form, redirects, integrations) — a one-line note on what was tested.

## 8. Access & Ownership

| Role | Who | Permission | Notes |
|---|---|---|---|
| Organization Owner | adeperthuis@gmail.com | Full control | Primary technical owner account, account of record for GitHub |
| Repository Admin | Arthur | Full control | |
| Migration/support | Christian | Admin or Maintainer | Invited via his own individual GitHub account, for the duration of migration and post-launch support; role reviewed and scaled down afterward |
| Future collaborators | TBD | **Write** only | Can branch + PR, cannot merge to `main` or change branch/repo settings, unless explicitly approved otherwise |
| Claude Code / AI-assisted edits | N/A | Operates as Arthur, via local commits + PRs | Never configured with direct push rights to `main` |

Recommended access model: **each person authenticates with their own
individual account.** Arthur owns the organization and repository
through his primary technical account (adeperthuis@gmail.com), kept
distinct from the business/project email (arthur@expertsmarketing.com),
which is not used for infrastructure logins. Christian is invited as a
collaborator through his own personal/professional GitHub account.
Shared logins, shared credentials, or forwarding access codes between
accounts are explicitly not used — individual accounts preserve
traceability (git history shows who made each change) and security
(any one person's access can be revoked without affecting anyone
else's).

Even Arthur merges via PR (not direct push to `main`), so that the
Preview-before-Production discipline is structural, not just a habit.

## 9. Secrets / Sensitive Values

Nothing in this repo requires GitHub Actions secrets or environment
variables — the Google Apps Script URL and hCaptcha site key are
client-side values baked into `contact.html` today and will remain so;
they are not secrets in the security sense (the Apps Script endpoint's
protection is server-side validation + hCaptcha + honeypot, not
obscurity). No GitHub Actions workflows are needed for this project at
current scope — Vercel's own GitHub integration handles build/deploy
without a separate CI config.

## 10. Future CI Considerations (optional, not required now)

If desired later, lightweight GitHub Actions could be added without
disrupting this plan:
- A broken-internal-link checker on PRs (re-running the kind of
  link-resolution audit already done manually during the August 2026
  architecture review).
- HTML validation / accessibility linting.
These are optional enhancements, not prerequisites for the migration.
