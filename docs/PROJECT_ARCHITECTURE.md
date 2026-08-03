# Experts Marketing Website — Architecture

Last reviewed: 2026-08-01
Primary technical owner (GitHub/Vercel account of record): adeperthuis@gmail.com
Business/project email (Experts Marketing SL): arthur@expertsmarketing.com
Status: Pre-migration (Plesk). This document describes both the current
state and the target state; it should be kept in sync as the migration
proceeds and updated whenever the architecture changes.

## 1. Summary

Experts Marketing's website is a 9-page static HTML/CSS/JS site with no
build step and no framework. It carries one live business function beyond
marketing content: the contact form, which routes leads through Google
Apps Script into Airtable and triggers an email notification. The site is
migrating from Plesk (FTP-style hosting) to a GitHub + Vercel pipeline so
that every change is version-controlled, previewable, and reversible.

## 2. Current State

```
Arthur (manual edits)
  → Plesk file manager / FTP
  → expertsmarketing.com (Apache, static files)
      → contact.html
          → Google Apps Script (Web App, doPost)
              → Airtable (leads table)
              → Email notification → arthur@expertsmarketing.com
```

Characteristics:
- No version control. Changes are made directly on the server; there is
  no history, no diff, no review step, and no rollback beyond whatever
  Plesk's own file backups (if any) provide.
- Redirects and caching are handled by `.htaccess` (Apache
  `mod_rewrite`, `mod_expires`, `mod_deflate`).
- The contact form is entirely client-side JS; it does not depend on
  Plesk or Apache in any way — it POSTs directly from the browser to a
  Google Apps Script Web App URL.
- DNS currently points expertsmarketing.com's web records at Plesk's
  hosting IP. MX/email records point wherever the mailbox for
  arthur@expertsmarketing.com and hello@expertsmarketing.com are hosted
  (not Plesk-dependent as far as the website is concerned).

## 3. Target State

```
Arthur
  → Claude Code (local edits, reviewed diffs)
  → GitHub Organization: ExpertsMarketing
      → Repository: expertsmarketing-website (private, main = source of truth)
          → Pull Request → Vercel Preview Deployment
              → Visual review & approval (Arthur)
              → Squash merge to main → Vercel Production Deployment
                  → www.expertsmarketing.com
```

Characteristics:
- GitHub `main` branch is the single source of truth for what is live.
  No file is ever edited directly "on the server" again.
- Every change gets a unique Preview URL before it can reach production
  — visual regressions are caught before customers see them.
- Vercel keeps a full deployment history; any previous production
  deployment can be promoted back instantly (see MIGRATION_PLAN.md,
  Rollback Strategy).
- `.htaccess` is retired from active use; its rules are reimplemented in
  `vercel.json`, and the original file is preserved for reference at
  `docs/legacy-plesk.htaccess` rather than discarded.
- The contact form → Apps Script → Airtable → email chain is unchanged.
  It is host-independent today and remains so after migration.
- Canonical production domain is **www.expertsmarketing.com**, matching
  the URLs already published in `sitemap.xml`. The apex domain
  (expertsmarketing.com) redirects to `www`.

## 4. Repository Structure (target)

```
expertsmarketing-website/
├── index.html
├── about.html
├── contact.html
├── results.html
├── privacy.html
├── services/
│   ├── seo.html
│   ├── geo-aeo.html
│   ├── paid-search-social.html
│   └── social-media.html
├── assets/
│   ├── styles.css
│   ├── images/
│   └── fonts/
├── robots.txt
├── sitemap.xml
├── vercel.json          # redirects + headers (replaces .htaccess) — created, not yet tested/deployed
├── docs/
│   ├── PROJECT_ARCHITECTURE.md
│   ├── MIGRATION_PLAN.md
│   ├── GITHUB_SETUP_PLAN.md
│   ├── VERCEL_SETUP_PLAN.md
│   └── legacy-plesk.htaccess   # reference copy of the original .htaccess — added in Phase 1
├── .gitignore
└── README.md
```

### Explicitly excluded from the repository

The following are legacy Plesk-side artifacts and must not be committed
to `expertsmarketing-website`. They are not part of the live site:

- `css/` and `img/` — leftover Plesk default "site not configured"
  placeholder assets (confirmed unreferenced by any real page during the
  August 2026 audit; `css/style.css` in particular is not even valid
  CSS — it's a stray HTML snapshot saved under a `.css` filename).
- `plesk-stat/` — Plesk's own statistics directory, if present on the
  server.
- `test/` — any Plesk test/staging directory, if present on the server.
- `updated-website.zip` and any other archive/backup files sitting in
  the Plesk file manager.
- Any `.bak` files or dated backup copies.

**Not excluded — kept intentionally:** `favicon.ico` at the repo root is
committed and tracked. None of the 9 live pages reference it directly —
they all declare `<link rel="icon" type="image/svg+xml"
href="assets/images/favicon.svg">`, which is the actual active favicon
— but `favicon.ico` is retained as a harmless fallback (some older
browsers and crawlers request `/favicon.ico` automatically regardless
of the declared `<link rel="icon">`). `vercel.json` includes an explicit
`Cache-Control` header rule for it.

Note: the local working copy available during the August 2026 audit did
not contain `plesk-stat/`, `test/`, or `updated-website.zip`. These are
included in the exclusion list per Arthur's instruction in case they
exist directly on the Plesk server outside the synced folder — this
should be confirmed by checking the Plesk file manager directly before
Phase 1 export (see MIGRATION_PLAN.md §3).

### Redirect catch-all warning

`vercel.json` (created 2026-08-01, see MIGRATION_PLAN.md §5 and
VERCEL_SETUP_PLAN.md §5) ends its `redirects` array with two broad
catch-all rules translated from the original `.htaccess`:
`/:lang(en|es|fr)/:path*` and `/:term(lexico|lexique|lexicon)/:path*`,
both redirecting to `/`. **Do not create future pages under `/en`,
`/es`, `/fr`, `/lexico`, `/lexique`, or `/lexicon`** without first
reviewing these two rules — anything under those path prefixes that
isn't explicitly matched by a more specific rule ahead of them in the
array will be silently redirected to the homepage.

## 5. Integration Architecture

### Contact form (lead generation — business-critical)

```
Visitor fills contact.html form
  → client-side JS validates honeypot + hCaptcha token
  → fetch() POST (JSON body, text/plain content-type to avoid CORS
    preflight) → Google Apps Script Web App URL
      → Apps Script doPost():
          → writes row to Airtable
          → sends email notification → arthur@expertsmarketing.com
      → returns {status: "success"} JSON
  → form JS shows confirmation message, resets form
```

This chain has no server-side component on the website's own host —
migrating the static site from Plesk to Vercel does not touch it. The
only thing that could break it is a change to allowed origins/hostname
restrictions on the hCaptcha or Apps Script side, which should be
explicitly tested post-launch (see MIGRATION_PLAN.md §7).

### Email

Notification email delivery is handled entirely by Google Apps Script's
own send-mail capability, not by the website host. expertsmarketing.com's
MX records govern where `arthur@` and `hello@` mail actually lands; these
must not be touched during the DNS cutover (see MIGRATION_PLAN.md §4).

## 6. Ownership & Access Model

| Area | Owner |
|---|---|
| GitHub organization (ExpertsMarketing) | adeperthuis@gmail.com — Owner |
| Repository (expertsmarketing-website, private) | Arthur — Admin |
| Vercel team/project | Arthur, via adeperthuis@gmail.com — Owner |
| DNS / domain registrar | Arthur (or whoever currently holds it) |
| Google Apps Script project | Arthur (existing owner, unchanged) |
| Airtable base | Arthur (existing owner, unchanged) |
| Business/project email (contact form notifications, business comms) | arthur@expertsmarketing.com |

Recommended access model:
- **Arthur** — Owner/Admin on both GitHub and Vercel, via his personal
  technical account, **adeperthuis@gmail.com**, designated as the
  primary technical owner for this project. **arthur@expertsmarketing.com**
  remains the business/project email for Experts Marketing — used for
  contact-form lead notifications and business communications — but is
  **not** the GitHub/Vercel account of record. Keeping these separate is
  a deliberate choice: it avoids mixing personal infrastructure access
  with a shared business mailbox.
- **Christian** — Admin or Maintainer on GitHub, and an appropriate
  member role on Vercel, for the duration of the migration and
  post-launch support period. Christian is invited using **his own
  individual account/email**, not a shared login and not the business
  account. Role should be revisited (scaled down or removed) once
  post-launch support concludes, per Arthur's judgment.
- **Future collaborators** — Write access only (branch + PR, no merge
  or admin rights) unless explicitly approved otherwise by Arthur.
- Each person authenticates with their own individual account on both
  GitHub and Vercel. Shared credentials, shared logins, or any form of
  email/code forwarding between accounts is explicitly **not** the
  recommended model — individual accounts preserve traceability (who
  made which change) and security (each person's access can be revoked
  independently without affecting anyone else).
- AI-assisted edits (Claude Code) operate through Arthur's local git
  access and PRs — never configured with direct push rights to `main`.

## 7. Non-Goals / Deliberate Simplicity

- No static site generator, no framework, no npm build step. The site
  is intentionally plain HTML/CSS/JS; introducing a build pipeline is
  out of scope unless a concrete need (e.g. a blog, see §8) requires it.
- No server-side rendering, no API routes, no database on the website
  side. Lead storage stays in Airtable via Apps Script.
- No CMS. Content edits happen via HTML edits through the Claude
  Code → PR → preview → merge workflow.

## 8. Future Blog Expansion

Recommended path when the blog becomes a priority:
- Add a `/blog/` directory of static HTML (or Markdown, if a lightweight
  generator like 11ty/Astro is introduced at that point — a decision to
  make when the need is real, not preemptively).
- Extend `sitemap.xml` and the shared nav/footer include pattern to
  cover blog posts.
- If post volume grows past a few dozen, revisit whether a build step
  (templating, RSS generation) is worth the added complexity — this
  would be a deliberate, documented architecture change, not a silent
  addition.
- Keep the same GitHub → Preview → Production workflow; blog posts are
  just more pages in the same repo, so no new infrastructure is implied
  by adding a blog.

## 9. Change Log

| Date | Change |
|---|---|
| 2026-08-01 | Initial architecture documentation drafted, pre-migration. |
| 2026-08-01 | Revised: business-account ownership, Christian's role, confirmed www canonical domain, expanded exclusion list. |
| 2026-08-01 | Revised: GitHub/Vercel primary technical owner changed to adeperthuis@gmail.com; arthur@expertsmarketing.com retained as business/project email only, not the infrastructure account of record. |
