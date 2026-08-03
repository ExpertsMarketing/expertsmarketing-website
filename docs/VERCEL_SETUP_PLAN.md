# Vercel Setup Plan

Status: DRAFT — no Vercel project has been created and no domain has
been attached. Plan for review, to be executed only after approval.
Primary technical owner (Vercel account of record): adeperthuis@gmail.com
Business/project email (Experts Marketing SL): arthur@expertsmarketing.com

## 1. Project Setup

- Import `ExpertsMarketing/expertsmarketing-website` from GitHub into
  Vercel (OAuth link between the GitHub org and a Vercel account/team
  under adeperthuis@gmail.com, the primary technical owner account).
- Framework preset: **Other** (static site, no framework detected —
  this is correct; do not let Vercel auto-select a framework preset
  that assumes a build step).
- Build command: none / empty.
- Output directory: repository root (`.`), since HTML files sit at the
  top level and in `services/`.
- Install command: none.

This means every deploy is effectively "copy the files as-is to the
edge," matching the current no-build-step reality of the site.

## 2. Preview Deployment Workflow

- Every push to a non-`main` branch, and every PR opened against
  `main`, automatically gets a unique Preview URL
  (`expertsmarketing-website-<hash>-<team>.vercel.app` or similar).
- Vercel posts this URL as a GitHub status check / PR comment
  automatically — no manual step needed to generate it.
- This Preview URL is where all visual review happens (per the working
  rule: "Preview before Production whenever possible"). Arthur reviews
  the live Preview URL in a browser before approving/squash-merging the
  PR.
- Preview deployments are ephemeral but retained in Vercel's deployment
  history — useful for comparing "what did this look like before" even
  after the branch is merged/deleted.

## 3. Production Deployment Workflow

- Production deploys are triggered by squash-merges to `main` — no
  separate manual "deploy" step; merging the PR *is* the deploy trigger.
- Vercel keeps a full history of every production deployment. Any past
  deployment can be re-promoted to production instantly via the Vercel
  dashboard ("Promote to Production" / rollback), independent of git —
  this is the fast rollback path described in MIGRATION_PLAN.md §7.
- Recommendation: treat "merge to `main`" as equivalent to "this is
  approved to go live" — i.e., approval happens at the Preview-review
  stage (before merge), not after.

## 4. Domain Configuration

- **Canonical production domain: www.expertsmarketing.com** (decided —
  matches the URLs already published in `sitemap.xml`).
- Add both `www.expertsmarketing.com` and the apex
  `expertsmarketing.com` as domains on the Vercel project, with the
  apex configured to redirect to `www`.
- Vercel auto-provisions and renews the SSL certificate (Let's Encrypt)
  once DNS is correctly pointed — no manual certificate management.
- DNS records required at the registrar (added only during
  MIGRATION_PLAN.md Phase 5, not before):
  - `www` → CNAME to `cname.vercel-dns.com` (or Vercel's current
    documented target at execution time).
  - Apex `@` → Vercel's provided A record (or ALIAS/ANAME if the
    registrar supports it), per Vercel's domain setup instructions at
    execution time.
  - **No changes to MX, SPF, DKIM, or other TXT records.**

## 5. `vercel.json` — Redirects and Headers

Created (2026-08-01) as part of Migration Phase 2, translating every one
of the 40 legacy `.htaccess` redirect rules against
`docs/legacy-plesk.htaccess` on a line-by-line basis. It has **not**
been tested on a Preview deployment or connected to any domain yet.
Structurally:

```json
{
  "redirects": [
    { "source": "/es/contacto", "destination": "/contact.html", "statusCode": 301 },
    { "source": "/es/contacto/", "destination": "/contact.html", "statusCode": 301 },
    { "source": "/:lang(en|es|fr)/:path*", "destination": "/", "statusCode": 301 },
    { "source": "/:term(lexico|lexique|lexicon)/:path*", "destination": "/", "statusCode": 301 }
  ],
  "headers": [
    {
      "source": "/assets/fonts/(.*)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/assets/images/(.*)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/assets/styles.css",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000" }]
    },
    {
      "source": "/favicon.ico",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000" }]
    }
  ]
}
```

Key decisions baked into the real file:

- **`"statusCode": 301` on every rule**, not Vercel's `"permanent": true"`
  shorthand — `permanent: true` actually issues an HTTP 308, not 301.
  301 was chosen to preserve the original `.htaccess` behavior
  (`RedirectMatch 301`) as exactly as possible.
- **No global `"trailingSlash"` setting** — that would be a site-wide
  behavior change. Instead, each of the 38 non-catch-all rules has two
  explicit `redirects` entries (bare path + trailing-slash variant),
  mirroring the original `/?$` optional-trailing-slash pattern per
  rule rather than site-wide. This is why the file contains 78 redirect
  entries, not 40.
- **The two catch-all rules stay last** in the array
  (`/:lang(en|es|fr)/:path*` and
  `/:term(lexico|lexique|lexicon)/:path*`), exactly matching their
  position as the last (least specific) rules in the original
  `.htaccess`. **Do not add future pages under `/en`, `/es`, `/fr`,
  `/lexico`, `/lexique`, or `/lexicon`** without reviewing these two
  rules first — anything under those prefixes not explicitly listed
  above them will be redirected to `/`.

This file lives in the repo root and is tested on Preview deployments
before it ever affects production (it takes effect on every deployment,
preview or production, since it's part of the committed code — which is
exactly why Preview testing catches redirect mistakes before go-live).
Testing must include query-string passthrough checks
(`/es/contacto?utm_source=test`, `/en/lexicon/seo?utm_source=test`) in
addition to the plain redirect-target checks — see
MIGRATION_PLAN.md §5 for the full testing method.

## 6. Environment Variables

None required. The Apps Script URL and hCaptcha site key are hardcoded
client-side values in `contact.html` today; there's no server-side
secret for Vercel to hold, since this is a pure static deployment with
no serverless functions. If a future need arises for a Vercel Serverless
Function (e.g., to proxy the Apps Script call server-side), environment
variables would be introduced then — not needed for this migration.

## 7. Rollback via Vercel

- Fastest rollback in the whole system: Vercel dashboard → Deployments →
  select any previous production deployment → "Promote to Production."
  Takes effect in seconds, no DNS change, no git revert needed.
- This is the primary reason Vercel's deployment history is valuable
  beyond git history — it lets you roll back the *built/deployed*
  artifact instantly, independent of whether you also want to revert
  the source in git.

## 8. Team / Ownership on Vercel

| Role | Who | Notes |
|---|---|---|
| Vercel Team/Account Owner | adeperthuis@gmail.com | Primary technical owner account, account of record for Vercel |
| Migration/support access | Christian | Invited to the Vercel team via his own individual account, for the duration of migration and post-launch support; role reviewed afterward |
| Future collaborators | TBD | Standard member role, invited individually if/when needed |

Preview URLs are shareable by link regardless of Vercel account access,
so occasional external reviewers (e.g. a designer) don't need a Vercel
account at all to look at a Preview.

## 9. Monitoring / Post-Deploy

- Vercel provides basic deployment status and build logs by default —
  sufficient given there's no build step to fail.
- No additional monitoring is required for this migration's scope;
  revisit if/when the blog expansion (PROJECT_ARCHITECTURE.md §8)
  introduces more moving parts.
