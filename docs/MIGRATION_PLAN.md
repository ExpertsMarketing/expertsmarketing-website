# Migration Plan — Plesk → GitHub → Vercel

Status: DRAFT — not yet executed. Nothing in this plan has been carried
out; it requires Arthur's explicit approval per step before execution.
Primary technical owner (GitHub/Vercel account of record): adeperthuis@gmail.com
Business/project email (Experts Marketing SL): arthur@expertsmarketing.com

## 1. Goals

- Zero unplanned downtime on www.expertsmarketing.com.
- Preserve all 40 existing `.htaccess` redirects (legacy multilingual
  URLs → current pages) so no inbound link or bookmark breaks.
- Preserve the contact form → Apps Script → Airtable → email chain
  exactly as-is.
- Preserve email delivery (MX records untouched).
- Maintain a working rollback path at every stage.
- Visual review of every change before it reaches production, from the
  very first migration commit onward.

## 2. Pre-Migration Checklist

- [ ] Confirm current DNS records for expertsmarketing.com (A/ALIAS/CNAME
      for `@` and `www`, MX, TXT/SPF/DKIM) and record them verbatim as a
      backup reference.
- [ ] Confirm registrar access (who can change DNS today).
- [ ] Confirm Google Apps Script Web App deployment is stable and its
      URL unchanged.
- [ ] Confirm Airtable base/table structure is documented separately
      (not covered by this doc — recommend a short Airtable schema note
      if one doesn't exist).
- [ ] Full local backup of the current live file tree (already have this
      via the connected folder) before any repo initialization.
- [ ] Log into the Plesk file manager directly and confirm whether
      `plesk-stat/`, `test/`, `updated-website.zip`, or other backup
      files exist on the server — these were not present in the locally
      synced copy used for the August 2026 audit, but must still be
      excluded if they exist server-side.
- [ ] Arthur's GitHub account is set up under his primary technical
      account, adeperthuis@gmail.com, ready to create the
      `ExpertsMarketing` organization.
- [ ] Christian's individual GitHub and Vercel accounts identified, ready
      to be invited (not shared credentials).
- [ ] Arthur has (or creates) a Vercel account under adeperthuis@gmail.com
      for simplest OAuth linking to the GitHub org.
- [ ] Confirmed: arthur@expertsmarketing.com is used only for business
      communications and contact-form notifications, not as a GitHub or
      Vercel login.

## 3. Migration Sequence

**Phase 0 — Documentation (this phase)**
Draft and approve the four architecture documents plus `.gitignore`.
No infrastructure changes. *(Complete as of this revision.)*

**Phase 1 — Repository creation**
Create the private GitHub org (`ExpertsMarketing`) and repo
(`expertsmarketing-website`) under adeperthuis@gmail.com, the primary
technical owner account. Push the
current site as the initial commit, excluding all legacy artifacts
listed in PROJECT_ARCHITECTURE.md §4 (`css/`, `img/`, `plesk-stat/`,
`test/`, `updated-website.zip`, backup files, root `favicon.ico`). Copy
the current `.htaccess` into `docs/legacy-plesk.htaccess` as a preserved
reference (not active — Vercel ignores it). The live Plesk site is
completely untouched during this phase — this only creates a copy in
GitHub. Invite Christian to the org/repo with Admin or Maintainer role,
using his own account.

**Phase 2 — `vercel.json` authored**
Translate every `.htaccess` rule into `vercel.json` `redirects` and
`headers` entries (see §5 below). This is done and tested entirely on
Preview deployments — production is still Plesk at this point.

**Phase 3 — Vercel project connected**
Import the repo into Vercel (under adeperthuis@gmail.com, with
Christian invited to the Vercel team via his own account). Every
push/PR now gets an automatic Preview URL. No domain is attached to
Vercel yet — Plesk is still serving www.expertsmarketing.com.

**Phase 4 — Preview validation**
Full manual review on the Preview URL:
- Every page loads, nav works, images/fonts load.
- Every legacy redirect URL (all 40) tested against the Preview
  deployment and confirmed to 301 to the right page.
- Contact form submitted end-to-end on Preview and confirmed to reach
  Airtable + trigger the email (see §6).
- Lighthouse/accessibility spot-check against current baseline.

**Phase 5 — DNS cutover**
Only after Phase 4 passes. Lower DNS TTL 24–48h in advance to shrink the
propagation window. Point `www` (canonical) and apex records at Vercel
per Vercel's domain instructions, with apex redirecting to `www`. **MX,
TXT, SPF, DKIM records are not touched.** See §4 for detail.

**Phase 6 — Production verification**
Run the full post-launch checklist (§8) against
www.expertsmarketing.com.

**Phase 7 — Decommission Plesk (delayed)**
Keep the Plesk hosting account active but idle for an agreed rollback
window (recommend minimum 2 weeks) before cancelling/decommissioning.
This costs little and is the cheapest possible insurance policy.

**Phase 8 — Cleanup commit**
Once production is confirmed stable, do a follow-up commit finalizing
documentation with the actual migration date and confirming no legacy
artifacts were missed.

## 4. DNS Migration Considerations

- **www.expertsmarketing.com is the canonical production domain**,
  matching the URLs already published in `sitemap.xml`. The apex
  (`expertsmarketing.com`) should redirect to `www`, configured once in
  Vercel's domain settings.
- **MX records are the single highest-risk item in this entire
  migration.** Many registrars offer a one-click "point everything at
  [host]" flow when adding a new host's nameservers — this must be
  avoided. Only the specific A/ALIAS/CNAME records for the web hostnames
  should change. If the domain currently uses the registrar's own DNS
  (not Vercel-managed nameservers), keep it that way and just repoint
  the two web records — this is the lowest-risk approach and is
  recommended over switching to Vercel-managed nameservers.
- Lower TTL to something short (e.g. 300s) on the web records at least
  24 hours before cutover, so that if a rollback is needed, the revert
  also propagates fast.
- After cutover, verify MX records are unchanged with a DNS lookup
  before considering the migration complete.

## 5. Redirect Migration: `.htaccess` → `vercel.json`

The original `.htaccess` is preserved unmodified at
`docs/legacy-plesk.htaccess` for reference — Vercel does not read
`.htaccess` at all, so this file is documentation-only, not functional
configuration.

It contains two categories of active rules that must be translated:

**A. 301 redirects (40 rules)** — legacy `/en/`, `/es/`, `/fr/`,
`/lexico|lexique|lexicon/` URLs from a previous multilingual version of
the site, collapsing to current pages. These map directly to
`vercel.json`'s `redirects` array, e.g.:

```json
{
  "redirects": [
    { "source": "/es/contacto", "destination": "/contact.html", "statusCode": 301 },
    { "source": "/es/contacto/", "destination": "/contact.html", "statusCode": 301 },
    { "source": "/en/lexicon/seo", "destination": "/services/seo.html", "statusCode": 301 },
    { "source": "/en/lexicon/seo/", "destination": "/services/seo.html", "statusCode": 301 },
    { "source": "/:lang(en|es|fr)/:path*", "destination": "/", "statusCode": 301 },
    { "source": "/:term(lexico|lexique|lexicon)/:path*", "destination": "/", "statusCode": 301 }
  ]
}
```

**Status code decision:** `vercel.json` uses `"statusCode": 301` on every
rule, not Vercel's shorthand `"permanent": true`. `permanent: true`
would actually issue an HTTP **308**, not a 301 — functionally
equivalent for SEO, but the explicit `statusCode: 301` was chosen to
preserve the original `.htaccess` behavior (`RedirectMatch 301`) as
exactly as possible rather than substitute an adjacent status code.

**Trailing slash handling:** `vercel.json` does **not** set a global
`"trailingSlash"` value, because that would be a site-wide behavior
change affecting every route, not just these 40 legacy paths. Instead,
each of the 38 non-catch-all rules has two explicit entries in
`vercel.json` — one for the bare path and one with a trailing slash —
directly mirroring the original `.htaccess` `/?$` (optional trailing
slash) pattern on a rule-by-rule basis. This brings the total redirect
entry count in `vercel.json` to 78 (38 rules × 2 variants, plus the 2
catch-all rules, which already match nested paths inherently and don't
need a separate trailing-slash variant).

**Catch-all warning:** the two catch-all rules
(`/:lang(en|es|fr)/:path*` and `/:term(lexico|lexique|lexicon)/:path*`)
are kept at the bottom of the `redirects` array, exactly as they were
last (and least specific) in the original `.htaccess`. **Do not create
future pages under `/en`, `/es`, `/fr`, `/lexico`, `/lexique`, or
`/lexicon`** without first reviewing these two rules — any new page
under those path prefixes will be silently redirected to `/` unless the
catch-alls are updated or a more specific rule is added ahead of them.

Every one of the 40 `.htaccess` redirect lines has an explicit
corresponding entry in `vercel.json` — this was done as a literal
line-by-line translation against `docs/legacy-plesk.htaccess`.
`vercel.json` now exists in the repo root (created in Phase 2) but has
**not** been tested on a Preview deployment or connected to any
domain yet.

**B. Caching/compression headers** (`mod_expires`, `mod_deflate`) — map
to `vercel.json`'s `headers` array using `Cache-Control`, covering
`assets/fonts/`, `assets/images/`, `assets/styles.css`, and
`favicon.ico`. Vercel also gzip/Brotli-compresses automatically at the
edge, so the `mod_deflate` equivalent is effectively free and doesn't
need explicit configuration.

Testing method: build a checklist of all 40 source paths (78 entries
counting trailing-slash variants), hit each one against the Preview
deployment, and confirm the resulting redirect target and status code
(301) match the current Plesk behavior exactly. In addition to the
redirect targets themselves, explicitly test query-string passthrough
using at least:
- `/es/contacto?utm_source=test` → should land on
  `/contact.html?utm_source=test`
- `/en/lexicon/seo?utm_source=test` → should land on
  `/services/seo.html?utm_source=test`

and spot-check that non-redirected paths (`/contact.html`,
`/services/seo.html`, `/about.html`) are unaffected by the two
catch-all rules.

## 6. Contact Form Validation Strategy (post-migration)

Because the form is entirely client-side and host-independent, the risk
is low, but it must still be explicitly verified rather than assumed:

1. On the Preview deployment, submit a real test lead through the form.
2. Confirm the row appears in Airtable within a reasonable delay.
3. Confirm the email notification arrives at arthur@expertsmarketing.com.
4. Confirm hCaptcha renders and validates (site key is domain-agnostic
   by default, but worth checking hCaptcha account settings don't
   restrict allowed domains/hostnames — if they do, the new Vercel
   preview and production hostnames need to be added).
5. Confirm the honeypot field still correctly no-ops on a simulated bot
   submission (fill the hidden field, confirm no submission occurs).
6. Repeat steps 1–3 once more against the live production domain after
   DNS cutover, since this is the first real-world confirmation on the
   actual domain.

## 7. Rollback Strategy

Rollback is layered, from fastest/cheapest to slowest:

1. **Vercel instant rollback** — if an issue is caught after a
   production deploy but DNS is already on Vercel, Vercel lets you
   re-promote any previous deployment instantly (no rebuild, no DNS
   change). This is the primary rollback mechanism once cutover has
   happened.
2. **DNS revert** — if Vercel itself has a platform-level issue (rare),
   revert the `www`/apex records back to Plesk's IP. This is why Plesk is
   kept idle-but-active for the rollback window (Phase 7) rather than
   cancelled immediately.
3. **Git revert** — for content-level mistakes (bad copy, broken layout)
   that aren't urgent enough for a full rollback, revert the specific
   commit/PR on `main` (squash-merged, so each PR is a single revertible
   commit), which triggers a new (correct) production deployment
   automatically.

Rollback triggers to watch for: broken redirects (404s on legacy URLs),
contact form failures, visual regressions not caught in preview, or
unexpected DNS/email disruption.

## 8. Post-Launch Verification Checklist

- [ ] All 9 pages load on www.expertsmarketing.com (index, about,
      contact, results, privacy, 4 service pages).
- [ ] Apex domain correctly redirects to `www`.
- [ ] All 40 legacy redirects return correct 301 + destination.
- [ ] `sitemap.xml` and `robots.txt` accessible and correct on the new
      host.
- [ ] Contact form full round-trip (Airtable + email) confirmed on
      production domain.
- [ ] Email delivery for arthur@ and hello@ unaffected (send/receive
      test).
- [ ] SSL/TLS certificate valid on production domain (Vercel
      auto-provisions via Let's Encrypt).
- [ ] Google Search Console / analytics (if any) still tracking
      correctly post-cutover.
- [ ] Spot-check mobile nav, fonts, images across at least 2 browsers.
- [ ] Confirm old Plesk hosting still reachable as rollback target (not
      yet cancelled).

## 9. Risk Register Summary

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| `.htaccess` redirects not fully replicated | Medium | Medium (SEO/broken bookmarks) | Line-by-line translation against `docs/legacy-plesk.htaccess` + full test pass before cutover |
| DNS change accidentally touches MX | Low | High (email outage) | Change only web records; verify MX post-cutover |
| hCaptcha domain restriction blocks new host | Low | Medium (form breaks) | Explicit form test on Preview + Production |
| Vercel platform issue at cutover | Low | Medium | Plesk kept idle as rollback target |
| Legacy Plesk cruft committed into repo | Low | Low | Explicit exclusion list in Phase 1; confirm server-side contents before export |
| Undiscovered server-side files (`plesk-stat/`, `test/`, zip/backups) not caught by local sync | Low–Medium | Low | Direct Plesk file-manager check added to Pre-Migration Checklist |
