# Iteration 3 — Website Requirements

Status: ACTIVE. First version: 2026-08-07. This is the permanent record
of the Iteration 3 change requests for www.expertsmarketing.com — the
original requirements, their implementation status audited against the
live codebase, and the decisions still outstanding. It sits alongside
`DECISIONS.md`, `LESSONS_LEARNED.md` and `ARCHITECTURE_HISTORY.md` in the
project knowledge base.

Where those documents record *how this project operates*, this one records
*what the site was asked to become* — so that requirements outlive the
chat threads they arrived in, and any future session can pick up the
remaining work without the source PDF in hand.

---

## 1. Document Control

| Field | Value |
|---|---|
| **Source document** | `26 05 12 Experts Marketing Website - Iteration 3.docx.pdf` (13 pages) |
| **Source location** | Supplied by Arthur from outside the repository |
| **Added to repository** | 2026-08-07 |
| **Extraction method** | `pdftotext -layout` |
| **Audited against** | `main` @ `b7d9f88` |
| **Status** | Living document — update the status column as work ships |

### Transcription policy

Requirement text is quoted **verbatim** from the source document,
including its original typos and inconsistent capitalisation (for example
"carrer sites", "Paid ads . Cost Per Lead"). Do not silently correct the
source text — corrections belong in the **Notes** field, so the record
stays checkable against the PDF.

The source is marked "AMENDED REQUESTS IN YELLOW". Yellow-highlighted
amendments supersede the earlier body text where the two conflict. Every
such conflict is recorded in [Section 6](#6-ambiguities--decisions-needed)
rather than being resolved silently.

### How to use this document

- Reference requirements by **ID** (for example "implement PAID-03")
  instead of re-quoting the PDF.
- IDs are stable and must never be renumbered. Superseded items keep
  their ID and are marked in place.
- When an item ships, update its Status and Evidence, and add a line to
  [Section 7](#7-change-log).

---

## 2. Status Legend

| Status | Meaning |
|---|---|
| ✅ **Complete** | Implemented and verified in the current `main` branch |
| 🟡 **Partial** | Partly delivered — the Notes field states exactly what remains |
| ⬜ **Open** | Not started |
| ⚠️ **Blocked** | Needs an asset or decision from Arthur before it can be built |

---

## 3. Summary Dashboard

| Page | ✅ | 🟡 | ⬜ | ⚠️ | Total |
|---|---|---|---|---|---|
| Homepage | 8 | 3 | 2 | 0 | 13 |
| GEO / AEO | 8 | 1 | 0 | 0 | 9 |
| SEO | 7 | 2 | 0 | 1 | 10 |
| Case Studies | 2 | 1 | 1 | 1 | 5 |
| Paid Ads | 0 | 0 | 7 | 0 | 7 |
| Social Media | 0 | 0 | 6 | 0 | 6 |
| SEO Optimization | 0 | 0 | 2 | 1 | 3 |
| **Total** | **25** | **7** | **18** | **3** | **53** |

Roughly **47% of Iteration 3 is delivered**, and the 28 items that are
not Complete are concentrated in two untouched pages plus the meta/URL
work.

**Largest remaining blocks of work:** the Paid Ads page and the Social
Media page are essentially untouched by Iteration 3 — both still carry
their pre-Iteration-3 "Service · 03" / "Service · 04" eyebrows and
original copy. The SEO Optimization section (meta titles, meta
descriptions, URL slugs) has not been started.

---

## 4. Requirements by Page

### 4.1 Homepage

Related page: [index.html](../index.html)

---

#### HP-01 · Hero background image

> "Following user feedback and testing with our targeted audience, we've
> found that the Barcelona background image in the hero section doesn't
> add value to the UX and potentially distracts from our core message. If
> you have already prepared a version for testing, please share the
> link/preview so we can review it. Otherwise, let's stick to the current
> clean approach with a plain background to maintain focus on the value
> proposition."

- **Status:** 🟡 Partial
- **Related pages:** [index.html:100](../index.html:100)
- **Evidence:** The hero still renders a five-slide Barcelona image
  carousel (`bcn-towers`, `bcn-encants`, `bcn-skyscrapers`,
  `bcn-museu-disseny`, `bcn-mix`) behind a `.hero-overlay` and
  `.hero-glass` panel.
- **Notes:** The requirement is conditional — it asks either to review a
  prepared version or to fall back to a plain background. The current
  carousel is neither. Needs a decision from Arthur; see
  [Section 6](#6-ambiguities--decisions-needed).

---

#### HP-02 · Navigation — visual treatment

> "Visual Treatment: Apply a more distinct style to the menu items so they
> don't look like 'static text' (e.g., hover effects or slight weight
> increase)."

- **Status:** ✅ Complete
- **Related pages:** [assets/styles.css](../assets/styles.css)
- **Evidence:** `.nav-links a` carries `font-weight: 500` and a
  `transition: color .15s ease`, with `.nav-links a:hover` and
  `.nav-links a.active` resolving to the darker `--ink`.

---

#### HP-03 · Navigation — link renames, order, CTA, replication

> "Rename 'Results' to 'Case Studies' / Rename 'About' to 'About us' /
> Structure: SEO | GEO / AEO | Paid ads I Social | Case Studies | About us
> Change order starting with GEO / AEO I SEO / CTA button in top
> navigation bar : 'Get Your AI Visibility Review' need to be in white /
> This navigation bar needs to be replicated for all pages"

- **Status:** ✅ Complete
- **Related pages:** all 9 HTML pages
- **Evidence:** Delivered by PR #11 ("Standardize header nav across all
  pages, GEO/AEO before SEO"). Canonical order is GEO / AEO, SEO, Paid
  Ads, Social, Case Studies, About us. The CTA uses `.btn-primary`, whose
  `color: #fff` renders the label in white.
- **Notes:** The source writes the structure with SEO first, then amends
  it to "Change order starting with GEO / AEO I SEO". The amendment was
  applied.

---

#### HP-04 · Hero tagline and blue-title typography

> "Tagline: Change 'GROWTH MARKETING · BUILT IN BARCELONA' to 'AI
> Marketing agency in Barcelona' (pending final search volume
> confirmation). All Taglines typography: Increase the font size of all
> blue titles for better visual hierarchy."

- **Status:** ✅ Complete
- **Related pages:** [index.html:128](../index.html:128), [assets/styles.css](../assets/styles.css)
- **Evidence:** The hero eyebrow reads "AI Marketing agency in Barcelona".
  Eyebrow font size was raised from 12px to 14px sitewide by PR #12.
- **Notes:** The tagline is marked "pending final search volume
  confirmation" in the source — treated as delivered, but revisit if the
  keyword research changes the wording.

---

#### HP-05 · Hero headline, sub-headline, CTAs, service links

> "Headline: Win the searches that drive revenue -- on Google and AI. /
> Sub-headline: Most companies invest in SEO and paid media -- yet remain
> invisible in AI-driven search. We fix that by combining SEO, paid
> acquisition, and AI visibility (GEO) into one execution plan focused on
> traffic, qualified leads, and conversion. / Primary CTA: Get Your AI
> Visibility Review / Secondary CTA: See How We Drive Results / Service
> Links: Add direct links to service pages below the CTAs for: SEO · GEO /
> AEO · Paid Ads · Social"

- **Status:** ✅ Complete
- **Related pages:** [index.html:129](../index.html:129)
- **Evidence:** Headline, sub-headline and both CTAs match the source. The
  `.hero-meta` block below the CTAs links to all four service pages.
- **Notes:** The service links render in the source order (SEO first),
  which differs from the GEO/AEO-first order applied to the main nav in
  HP-03. Flagged in [Section 6](#6-ambiguities--decisions-needed).

---

#### HP-06 · Logo bar

> "Add Title: 'Trusted by teams at:' / Update: Use official/high-resolution
> logos only. This part is unclear Integrate the Official logos as
> displayed below, the logo background may need to be transparent in order
> to look clean in the grey section. THis section needs to be straight
> below the logos"

- **Status:** ✅ Complete
- **Related pages:** [index.html:147](../index.html:147)
- **Evidence:** `.logos-label` reads "Trusted by teams at:". Seven
  normalised WebP logos are in place (Microsoft, Indeed, Hertz, Medsir,
  Voyage Privé, Vueling, Danone). The client-results section sits directly
  below the logo strip.

---

#### HP-07 · Client results — replace Uptoled block with Medsir

> "Change the block dedicated to Uptoled by
> -40%
> Paid ads . Cost Per Lead
> Medsir. High-intent Google Ads targeting and landing page optimization
> increased qualified clinical trial opportunities across Europe.
> Review our case study  (bigger font )"

- **Status:** ⬜ Open
- **Related pages:** [index.html:222](../index.html:222), [results.html:59](../results.html:59)
- **Evidence:** Both pages still show the Uptoled block. The homepage reads
  `-30%` / "Uptoled. Campaign restructure and creative iteration dropped
  cost per lead while maintaining lead quality." The Case Studies KPI row
  reads `−30%` / "Uptoled."
- **Notes:** Depends on CS-04 (the Medsir case study section) for its
  anchor target. The Case Studies KPI row uses terse descriptions, so it
  takes "Medsir." rather than the full sentence. Note the two pages use
  different minus characters — hyphen `-30%` on the homepage, Unicode
  minus `−30%` on Case Studies; pick one for the new `-40%`.

---

#### HP-08 · Client results — block order

> "The block +10% GEO .... Indeed need to be the first one on the left,
> then +14% SEO ... Microsoft, then -40% Medsir"

- **Status:** ⬜ Open
- **Related pages:** [index.html:215](../index.html:215), [results.html:53](../results.html:53)
- **Evidence:** Both pages currently order the blocks Microsoft, Uptoled,
  Indeed. Required order is Indeed, Microsoft, Medsir.

---

#### HP-09 · Case study link — wording and font size

> "Each 'Read our case study ->' need to be in bigger font"
> "Review our case study  (bigger font )"

- **Status:** 🟡 Partial
- **Related pages:** [assets/styles.css:829](../assets/styles.css:829), [index.html:220](../index.html:220)
- **Evidence:** `.kpi-link` is `font-size: 16px`. The homepage links read
  "Read the case study" with no arrow; the GEO/AEO and SEO service pages
  use "Review our case study →".
- **Notes:** Two separate asks — a font-size increase and consistent
  wording. `.kpi-link` is shared across the homepage, GEO/AEO and SEO
  pages, so a change to it affects all three. The source uses three
  different labels ("Read our case study ->", "Review our case study",
  "Read the case study"); see
  [Section 6](#6-ambiguities--decisions-needed).

---

#### HP-10 · Client results — content and bold treatment

> "change GEO/SEO optimized pages -- Microsoft. by Microsoft: GEO/SEO
> optimized page (Bold Microsoft) / Microsoft in bold / Same format for
> Medsir: / Same format for Indeed:"

- **Status:** 🟡 Partial
- **Related pages:** [index.html:216](../index.html:216)
- **Evidence:** Each KPI block leads its description with the client name
  ("Microsoft.", "Indeed."), but the name is not wrapped in `<strong>` or
  otherwise emboldened.
- **Notes:** Should be handled together with HP-07 and HP-08 in a single
  pass over the KPI grid.

---

#### HP-11 · Services section

> "SERVICES / Four channels. One growth objective. / SEO: We improve your
> visibility on high-intent searches through: remove what is after:
> technical content...work. / GEO / AEO (AI Visibility): Get recommended by
> Chat GPT, Claude, Google AI remove what is after: Answer Engine...
> tracking / Result: Bold Result:"

- **Status:** ✅ Complete
- **Related pages:** [index.html:161](../index.html:161)
- **Evidence:** Section title "SERVICES", heading "Four channels. One
  growth objective.", four service cards with the specified bullets, and
  each result line rendered as `<strong>Result:</strong>`.
- **Notes:** The two "remove what is after" truncation instructions were
  not applied literally — the SEO card still ends "through technical,
  content, and authority work" and the GEO card still ends "Answer Engine
  Optimization and Share of Voice tracking". Both read as intended prose
  rather than as leftovers, so this is recorded as delivered; raise it if
  Arthur wants the literal truncation.

---

#### HP-12 · Testimonial role updates

> "Change 'CEO · DTC' to 'CEO · B2C services' / Change 'Head of Growth ·
> marketplace' to 'Head of Growth · Retail marketplace'"

- **Status:** ✅ Complete
- **Related pages:** [index.html:251](../index.html:251)
- **Evidence:** The cites read "Head of Growth · Retail marketplace" and
  "CEO · B2C services". The "Three things we hear from every CMO..."
  section is retained as instructed.

---

#### HP-13 · Footer and Privacy page

> "Below the logo Replace 'Growth marketing for brands that want to win in
> search -- and in what comes next. Built in Barcelona.' With 'Win the
> searches that drive revenue -- on Google and AI.' [...] Add the Follow us
> on Linkedin [...] Privacy · Imprint Remove '· Imprint' / For Privacy
> Create a Privacy page with content that follows GDPR agreement [...]
> Change Experts Marketing SL, registered in Barcelona / Experts Marketing
> registered in Barcelona under VAT number ES X8481273T"

- **Status:** ✅ Complete
- **Related pages:** [index.html:271](../index.html:271), [privacy.html](../privacy.html)
- **Evidence:** Footer tagline updated; three link columns present;
  LinkedIn link points to
  `https://www.linkedin.com/company/experts-marketing`; no "Imprint"
  string remains anywhere; the Privacy page exists with GDPR content and
  carries VAT number ES X8481273T with no "Experts Marketing SL"
  references.
- **Notes:** The source asks to "review if there is a direct link for user
  to automatically being add as a page member when clicking" — LinkedIn
  provides no such auto-follow URL, so the standard company-page link is
  used.

---

### 4.2 GEO / AEO Page

Related page: [services/geo-aeo.html](../services/geo-aeo.html)

---

#### GEO-01 · Header and hero

> "Tagline: Replace 'SERVICE · 02' by 'GENERATIVE ENGINE OPTIMISATION
> AGENCY' / Headline: Get cited by the engines that answer. / Sub-headline:
> AI platforms (ChatGPT, Perplexity, Google AI Overviews, Gemini, and
> Copilot) increasingly answer questions directly. We make sure when they
> do, your brand is the source -- not your competitor's. / Primary CTA:
> Change 'Get your visibility audit' to 'Get your AI visibility review' /
> Secondary CTA: 'See How We Drive Results'"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:38](../services/geo-aeo.html:38)
- **Evidence:** All five elements match the source text exactly.

---

#### GEO-02 · Expertise and vision section

> "Section Title: 'OUR UNIQUE GEO EXPERTISE' / Section Sub-title: 'Built to
> make AI engines recognize your brand as a trusted source.' remove
> 'engines' in ... make AI engines recognize... / Description Update:
> Replace existing text with: We focus on strengthening the signals AI
> engines actually use to select, trust, and cite brands in their answers.
> [...]"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:59](../services/geo-aeo.html:59)
- **Evidence:** Eyebrow reads "OUR UNIQUE GEO EXPERTISE"; the sub-title
  reads "Built to make AI recognize your brand as a trusted source" with
  "engines" removed as amended; the description paragraph matches.

---

#### GEO-03 · KPI block — supporting copy

> "On the right of the 10% AI Citation Rate (Indeed) add Our GEO/AEO
> Optimization Content restructuring for French and German career pages
> increased AI citation frequency across ChatGPT and Perplexity."

- **Status:** 🟡 Partial
- **Related pages:** [services/geo-aeo.html:70](../services/geo-aeo.html:70)
- **Evidence:** The KPI block shows "+10%" and "AI citation rate (Indeed)"
  with the centred case-study link, but the "Our GEO/AEO Optimization"
  heading and its supporting sentence are absent.
- **Notes:** The only outstanding item on this page.

---

#### GEO-04 · KPI block — case study link

> "Review our case study -> needs to be be placed in the middle and bigger
> font" / "Review our case study [Link to Case Study Page, directly the
> related paragraph in the case study page]"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:74](../services/geo-aeo.html:74)
- **Evidence:** `<a href="../results.html#case-indeed" class="kpi-link
  kpi-link-center">Review our case study →</a>` — centred via
  `.kpi-link-center` and deep-linked to the Indeed case study.
- **Notes:** The "bigger font" half of this request is tracked under HP-09,
  since `.kpi-link` is shared.

---

#### GEO-05 · Spacing between KPI block and GEO services

> "Reduce space between the KPI block and GEO services , you need to have
> the same space as between the other blocks"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:55](../services/geo-aeo.html:55)
- **Evidence:** The KPI section uses `padding-top: 0` with a
  `margin: 0 auto 40px` on the grid, and the following section also uses
  `padding-top: 0`.

---

#### GEO-06 · GEO services methodology — phase blocks

> "Section Title: 'OUR GEO SERVICES' / Phase 01 · AI Visibility Audit
> [...] Phase 02 · Content & Entity Optimization [...] Phase 03 · AI
> Authority Growth [...] Action: Add the CTA button below these blocks:
> 'Get your AI visibility review'"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:94](../services/geo-aeo.html:94)
- **Evidence:** All three phase blocks carry the specified bullets and
  Outcome lines, followed by the "Get your AI visibility review →" CTA.

---

#### GEO-07 · List markers — arrows, no indentation

> "Left Alignment with 'Micro-icons' or 'Arrows' (Apple Style) [...] we
> remove the classic black bullet point and the offset (indentation). The
> text starts directly beneath the first letter of the title for a 100%
> perfect vertical alignment. We replace the bullet with a micro-arrow or
> an ultra-thin checkmark that matches the blue color of your borders."

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:98](../services/geo-aeo.html:98), [assets/styles.css](../assets/styles.css)
- **Evidence:** Delivered by PR #15. The `.icon-list` class sets
  `list-style: none`, `padding: 0`, and an `::before` marker of `\2192`
  (→) in `var(--accent-deep)`.

---

#### GEO-08 · Layout cleanup

> "Visuals: Remove the picture. / Sections to Delete: Remove the entire
> 'HOW ENGAGEMENTS EVOLVE' section (with its 3 blocks) and the 'HOW GEO
> DIFFERS' section (including the title and comparison table)."

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html](../services/geo-aeo.html)
- **Evidence:** Neither "HOW ENGAGEMENTS EVOLVE" nor "HOW GEO DIFFERS"
  appears on the page.

---

#### GEO-09 · FAQ and final CTA

> "Section Title: Change 'FAQ' to 'FAQ about GEO' / Section Sub-title:
> Change 'What people ask...' to 'What clients ask before starting.' [5
> updated Q&As] / Final CTA Block: Title 'AI VISIBILITY REVIEW' [...] CTA
> Button: Change to 'Get your AI visibility review'"

- **Status:** ✅ Complete
- **Related pages:** [services/geo-aeo.html:141](../services/geo-aeo.html:141)
- **Evidence:** Eyebrow "FAQ about GEO", sub-title "What clients ask before
  starting.", all five Q&As present with the source wording, and the final
  CTA banner matches.

---

### 4.3 SEO Page

Related page: [services/seo.html](../services/seo.html)

---

#### SEO-01 · Header and hero

> "Tagline: Replace 'SERVICE · 02' by 'INTERNATIONAL SEO AGENCY' /
> Headline: Replace 'SEO that compounds' by 'SEO that turns visibility into
> revenue.' / Sub-headline: [...] / Primary CTA: Change 'Start a
> conversation' to 'Get your SEO visibility review' / Change secondary CTA
> 'How we work' by 'See How We Drive Results' (linking to Cases studies >
> Microsoft Teams block"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:38](../services/seo.html:38)
- **Evidence:** All elements match; the secondary CTA deep-links to
  `../results.html#case-microsoft`.

---

#### SEO-02 · Client results block — supporting copy

> "On the right of the 5% TRIAL CONVERSION -MICROSOFT +5 % ...... add Our
> SEO/GEO Optimization On-page optimization across 9 international markets
> lifted organic traffic and trial conversions"

- **Status:** 🟡 Partial
- **Related pages:** [services/seo.html:151](../services/seo.html:151)
- **Evidence:** The KPI grid shows "+14% / Organic growth — Microsoft" and
  "+5% / Trial conversion — Microsoft" with descriptions that restate the
  figures. The "Our SEO/GEO Optimization" heading and its sentence are
  absent.
- **Notes:** Directly parallel to GEO-03; both should be done together.

---

#### SEO-03 · Client results block — placement

> "Just below integrate this block Client results"

- **Status:** 🟡 Partial
- **Related pages:** [services/seo.html:151](../services/seo.html:151)
- **Evidence:** The KPI block exists but sits after the services and
  process sections, not directly below the hero as the source specifies.
  It also lacks a "Client results" eyebrow.
- **Notes:** Recorded separately from SEO-02 because it is a layout move
  rather than a copy addition.

---

#### SEO-04 · Services structure

> "Section Title: Change 'WHAT WE DO' to 'OUR SEO SERVICES' / Section
> Sub-title: [...] 'One growth objective.' / Description Update [...] /
> Technical SEO [...] SEO Content Optimization [...] Authority Building
> [...] Add CTA 'Get your SEO visibility review'"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:55](../services/seo.html:55)
- **Evidence:** Eyebrow "OUR SEO SERVICES", heading "One growth
  objective.", the replacement description paragraph, all three service
  cards with their five bullets each, and the CTA below.

---

#### SEO-05 · "SEO Content Optimization" title on one line

> "Title 'SEO Content Optimization' need to be in one line so that the list
> below is not lower than other service lists"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:83](../services/seo.html:83)
- **Evidence:** The heading carries the `card-title-fit` class.

---

#### SEO-06 · List markers — arrows, no indentation

> "Left Alignment with 'Micro-icons' or 'Arrows' (Apple Style) [...]"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:73](../services/seo.html:73)
- **Evidence:** Delivered by PR #15 via the shared `.icon-list` class. See
  GEO-07.

---

#### SEO-07 · Trust and case studies — metrics

> "Metrics Update: Replace current KPIs ( +340%...) with: +14% organic
> growth on GEO/SEO optimized pages (Microsoft) / +5% conversion of organic
> visits to trial download (Microsoft) / Review our case study [Link to
> Case Study Page, directly to the related paragraph in the case study
> page]"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:153](../services/seo.html:153)
- **Evidence:** The `+340%` placeholder is gone; both Microsoft KPIs are
  present with a centred link to `../results.html#case-microsoft`.

---

#### SEO-08 · Process and methodology

> "Section Title: Change 'Three phases. One direction.' to 'Three SEO
> phases. One direction.' / Copy Update: Remove the sentence: 'We don't
> sell packages.' / Phase 1 Title: Change 'Audit & baseline' to 'SEO Audit
> & baseline' / Action: Add the CTA button below these blocks: 'Get your
> SEO visibility review'"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:112](../services/seo.html:112)
- **Evidence:** Heading reads "Three SEO phases. One direction."; the
  "We don't sell packages." sentence is absent; Phase 01 is titled "SEO
  Audit & baseline"; the CTA is present.

---

#### SEO-09 · Testimonials

> "Section Title: 'Real Growth, Measured: What Our Partners Say' / Jaroslav
> Pavlik [...] (Use LinkedIn Photo) / Yansong Sun [...] (Use LinkedIn
> Photo) / Johary Rasolofomanana [...] (Use LinkedIn Photo) / Add CTA: 'Get
> your SEO visibility review' / Each testimonial in each block should be
> horizontal in landscape format using the full width of the screen so that
> is will be easily readable"

- **Status:** ⚠️ Blocked
- **Related pages:** [services/seo.html:170](../services/seo.html:170)
- **Evidence:** The section title, all three quotes, all three
  attributions and the CTA are in place. However, the testimonials render
  in a three-column `grid grid-3` (portrait cards), not the requested
  full-width landscape format, and no LinkedIn photos are present.
- **Notes:** Blocked on two inputs from Arthur — the LinkedIn profile
  photos (which need permission from each person to reuse) and
  confirmation of the landscape layout. One quote also differs from the
  source: the site reads "Their SEO/GEO best practices" where the source
  reads "His SEO/GEO best practices"; the site's wording is the better
  one and is assumed intentional.

---

#### SEO-10 · FAQ and final CTA

> "Section Title: Change 'FAQ' to 'FAQ about SEO' / Section Sub-title:
> [...] 'What clients ask before starting.' [7 updated Q&As] / Final CTA
> Block keep it as it is, just change CTA Button: Change from 'Get your
> visibility audit' to 'Get your visibility review'"

- **Status:** ✅ Complete
- **Related pages:** [services/seo.html:195](../services/seo.html:195)
- **Evidence:** Eyebrow "FAQ about SEO", sub-title "What clients ask before
  starting.", all seven Q&As present, and the final CTA reads "Get your
  visibility review".

---

### 4.4 Case Studies Page

Related page: [results.html](../results.html)

---

#### CS-01 · KPI row

> "Use the same KPIs as the one presented in the home page / +14% organic
> growth on GEO/SEO optimized pages · Microsoft / Replace / -30% Cost Per
> Lead with Paid ads · Uptoled replace by: / -40% Cost Per Lead with Paid
> ads · Medsir / +10% AI citation rate · Indeed"

- **Status:** 🟡 Partial
- **Related pages:** [results.html:53](../results.html:53)
- **Evidence:** The KPI row exists with all three blocks, but still shows
  "−30% / Uptoled." and orders them Microsoft, Uptoled, Indeed.
- **Notes:** Same change as HP-07 and HP-08, applied to this page. This
  row uses terse descriptions and currently has no case-study links.

---

#### CS-02 · Indeed case study

> "Case Study GEO / AI Visibility · Indeed France & Germany [...] Challenge
> [...] Our GEO Optimization [...] Results [...]"

- **Status:** ✅ Complete
- **Related pages:** [results.html:80](../results.html:80)
- **Evidence:** `#case-indeed` carries the Challenge / Our GEO Optimization
  / Results three-column structure with the source content.
- **Notes:** The source's typo "carrer sites" is correctly rendered as
  "career sites" on the site.

---

#### CS-03 · Microsoft case study

> "SEO · Microsoft (Teams) [...] Challenge [...] Our SEO Optimization [...]
> Results [...]"

- **Status:** ✅ Complete
- **Related pages:** [results.html:115](../results.html:115)
- **Evidence:** `#case-microsoft` carries the full three-column structure
  with the source content.
- **Notes:** The source lists "+ 3% conversion of organic visits to trial
  download" here, while the SEO page KPI (SEO-07) specifies "+5%
  conversion". Both figures appear in the source document. See
  [Section 6](#6-ambiguities--decisions-needed).

---

#### CS-04 · Medsir case study — NEW SECTION

> "GOOGLE ADS -- Medsir
> Generating qualified clinical trial opportunities across Europe
> -40%
>
> **Challenge**
> Medsir needed to attract researchers, biotech companies, and healthcare
> innovators seeking support for clinical trials in Europe. The challenge
> was identifying high-intent searches linked to active treatment projects
> while filtering out broader academic and healthcare queries with low
> commercial value.
>
> **Our Google Ads Optimization**
> We developed a search strategy focused on qualified clinical research
> demand:
> - Identification of high-intent clinical trial keywords
> - Segmentation by project maturity and lead quality
> - Country-specific campaigns adapted to local terminology and search
>   behavior
> - Continuous optimization of ad messaging and landing pages
> - Testing of webinars, white papers, expert consultations, and contact
>   forms
>
> **Results**
> - -40% cost per lead in 12 months
> - +15% landing page conversion rate
> - 4% CTR across targeted campaigns
> - Higher volume of qualified opportunities from researchers and biotech
>   companies
> - Scalable acquisition framework deployed across multiple European
>   markets"

- **Status:** ⬜ Open
- **Related pages:** [results.html:145](../results.html:145)
- **Evidence:** No Medsir case study exists. The page has only
  `#case-indeed` and `#case-microsoft`.
- **Notes:** Proposed anchor ID **`#case-medsir`**, matching the existing
  convention. Should mirror the `.card` structure of CS-02 and CS-03
  (eyebrow line with service and figure, `<h3>` headline, then a
  `grid grid-3` of Challenge / Our Google Ads Optimization / Results).
  This section is the link target for HP-07, CS-01 and PAID-03.

---

#### CS-05 · Case study visuals

> "For the section selected work / recent engagements you need to add the
> screenshot visuals to illustrate and keep the structure Challenge / our
> (Service) Optimization / Results" / "Indeed citation quote in Gemini for
> the query 'How to write a motivation letter'" / "You can use a screenshot
> as an illustration:
> https://www.microsoft.com/en-gb/microsoft-teams/group-chat-software"

- **Status:** ⚠️ Blocked
- **Related pages:** [results.html:80](../results.html:80)
- **Evidence:** No screenshots appear in any case study; all three are
  text-only.
- **Notes:** Blocked on Arthur supplying the image assets. The Gemini
  citation screenshot in particular cannot be reconstructed — it is a
  point-in-time capture of an AI answer. Check reuse rights before
  publishing third-party product screenshots.

---

### 4.5 Paid Ads Page

Related page: [services/paid-search-social.html](../services/paid-search-social.html)

> **Whole-page status: not started.** Every requirement below is Open. The
> page still carries its pre-Iteration-3 copy.

---

#### PAID-01 · Global brand and header styles

> "Replace: SERVICE · 03 By: PERFORMANCE MARKETING AGENCY / Replace:
> 'Performance media, built on unit economics.' By: Paid campaigns built to
> generate qualified leads. / Replace: 'Google, Meta, LinkedIn and TikTok
> -- run by people who care about CAC and LTV more than dashboards. [...]'
> By: We optimize the full acquisition journey -- targeting, ads, landing
> pages, and conversion paths -- to turn paid traffic into qualified leads
> and measurable business growth. / CTA Change Replace: Start a
> conversation By: Get your SEO & paid growth assessment / Remove: 'How we
> work' button"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:40](../services/paid-search-social.html:40)
- **Evidence:** The eyebrow still reads "Service · 03".

---

#### PAID-02 · What We Do section

> "Replace: WHAT WE DO By: Our paid and social ads services / Replace:
> 'Every lever that moves CAC.' By: More than campaigns. A system built to
> convert. / Replace: 'Bidding gets you ten percent. [...]' By: Most
> agencies focus on campaign management. We optimize the full conversion
> path -- targeting, creatives, landing pages & tracking because paid
> performance depends on what happens before & after the click."

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:58](../services/paid-search-social.html:58)
- **Evidence:** Still reads "What we do" and "Every lever that moves CAC."

---

#### PAID-03 · Client results block (Medsir)

> "Client Results (Insert just below) as you did for GEO and SEO page
> Client results
> -40%
> Paid ads . Cost Per Lead
> on the right
> Medsir. High-intent Google Ads targeting and landing page optimization
> increased qualified clinical trial opportunities across Europe.
> center below Review our case study  (bigger font )"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:58](../services/paid-search-social.html:58)
- **Evidence:** No client-results block exists on this page.
- **Notes:** Mirrors the GEO/AEO and SEO page treatment. Links to
  `#case-medsir` (CS-04).

---

#### PAID-04 · Services section

> "Replace the 3 service blocks with: 1. Search campaigns built around
> buying intent [...] 2. Paid social focused on audience quality [...]
> 3. Landing pages that improve conversion [...]"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:68](../services/paid-search-social.html:68)
- **Evidence:** Current blocks are "Paid Search", "Paid Social" and
  "Creative & landing pages" with the old copy.

---

#### PAID-05 · Engagement model

> "HOW ENGAGEMENTS EVOLVE / Phase 01 -- Account Audit & Baseline [...]
> Phase 02 -- Conversion Optimization [...] Phase 03 -- Scaling &
> Performance Growth [...]"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:99](../services/paid-search-social.html:99)
- **Evidence:** The three phases exist but carry the old titles and
  paragraph copy rather than the specified bulleted lists and Outcome
  lines.

---

#### PAID-06 · Remove KPI block

> "KPI Section -- Remove entire block: -42% COST PER ACQUISITION (DTC
> retailer case) / +157% ROAS ON GOOGLE ADS (Marketplace client case)"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:131](../services/paid-search-social.html:131)
- **Evidence:** Both KPI blocks are still present.
- **Notes:** These cite unnamed "DTC retailer" and "Marketplace client"
  cases; removing them also removes unattributed performance claims.

---

#### PAID-07 · FAQ section

> "Replace: FAQ By: FAQ ABOUT PAID ADS / Replace all FAQs with: [7 new
> Q&As, from 'What makes a performance marketing campaign profitable?'
> through 'What's the Paid ads contract length?']"

- **Status:** ⬜ Open
- **Related pages:** [services/paid-search-social.html:148](../services/paid-search-social.html:148)
- **Evidence:** The eyebrow still reads "FAQ" with the original Q&As.
- **Notes:** Full replacement text is in the source document, pages 10–11.

---

### 4.6 Social Media Page

Related page: [services/social-media.html](../services/social-media.html)

> **Whole-page status: not started.** Every requirement below is Open.

---

#### SOC-01 · Positioning and hero

> "Replace SERVICE · 04 With SOCIAL MEDIA MARKETING AGENCY / Replace
> 'Organic social, treated as editorial.' With 'Social media built to earn
> mentions.' / Replace 'We don't post for the sake of posting. [...]' With
> 'We don't post for the sake of posting. We build editorial systems across
> LinkedIn, Instagram and TikTok designed to generate attention,
> conversation and mentions -- the social signals that platforms and AI
> engines increasingly prioritize when surfacing brands.' / Replace CTA
> 'Start a conversation' With 'Get your social media review'"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:40](../services/social-media.html:40)
- **Evidence:** Still reads "Service · 04", "Organic social, treated as
  editorial." and "Start a conversation".

---

#### SOC-02 · Unique value proposition section

> "Replace WHAT WE DO With OUR UNIQUE SOCIAL MEDIA MANAGEMENT SERVICES /
> Replace 'Social is a long compounding game. [...] not vanity counts.'
> With '[...] a cadence that builds genuine reach and mentions, not vanity
> counts.' / After the 3 blocks add CTA button Get your social media
> review"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:58](../services/social-media.html:58)
- **Evidence:** Still reads "What we do".

---

#### SOC-03 · Engagement process section

> "Replace HOW ENGAGEMENTS EVOLVE With HOW SOCIAL ENGAGEMENTS EVOLVE /
> Replace 'Social engagements need patience and rhythm. [...]' With
> 'Impactful social engagements need patience and rhythm. [...]'"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:100](../services/social-media.html:100)
- **Evidence:** Still reads "How engagements evolve".

---

#### SOC-04 · Three process blocks

> "Phase 01 · Foundations / Strategy & Voice [...] Outcome: Clear
> positioning, consistent messaging across channels, and a content strategy
> aligned with buyer intent, category visibility, and long-term brand
> recognition. / Phase 02 · Build Cadence / Content Production at Scale
> [...] / Phase 03 · Compound / Audience & Influence [...]"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:111](../services/social-media.html:111)
- **Evidence:** The three phases exist with older titles and no Outcome
  lines. Phase 02 is titled "Content production at rhythm" rather than
  "Content Production at Scale".

---

#### SOC-05 · Section CTA

> "Add CTA after the section Get your social media review"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:127](../services/social-media.html:127)
- **Evidence:** No CTA follows the phases section.

---

#### SOC-06 · FAQ and final CTA

> "Replace FAQ With FAQ ABOUT SOCIAL MEDIA SERVICES / Replace 'What people
> ask before starting.' With 'What clients ask before starting.' / [5 new
> FAQs] / Final CTA Replace 'Get your audit' With 'Get your social media
> review'"

- **Status:** ⬜ Open
- **Related pages:** [services/social-media.html:132](../services/social-media.html:132)
- **Evidence:** The eyebrow still reads "FAQ" and the final CTA still
  reads "Get your audit".
- **Notes:** Full replacement FAQ text is in the source document,
  pages 12–13.

---

### 4.7 SEO Optimization (Meta and URLs)

> **Whole-section status: not started.**

---

#### META-01 · Meta titles for all pages

> "Length must be BETWEEN 55 and 60 characters maximum. NEVER exceed 60
> characters. Do NOT include the brand name 'Experts Marketing'. Use the
> MOST RELEVANT and HIGHEST SEARCH VOLUME keyword [...] Each title must be
> unique."

- **Status:** ⬜ Open
- **Related pages:** all 9 HTML pages
- **Evidence:** Every current title includes the brand name "Experts
  Marketing" and most fall short of 55 characters — for example
  `<title>SEO — Experts Marketing</title>` (23 characters).

---

#### META-02 · Meta descriptions for all pages

> "Length must be BETWEEN 155 and 160 characters maximum. NEVER exceed 160
> characters. Must include: the client pain point or business need, the
> unique value proposition, a strong CTA."

- **Status:** ⬜ Open
- **Related pages:** all 9 HTML pages
- **Evidence:** Current descriptions run from 57 to 134 characters — every
  one falls short of the 155-character floor — and none carry a CTA. The
  GEO/AEO description is 65 characters; the Case Studies page is 57.

---

#### META-03 · URL slug optimization

> "All priority 1 keyword need to be part of the slug (final part of the
> url - For example if 'GEO optimization' is P1 kw the url should be
> 'http://www.expertsmarketing.com/services/geo-optimization'"

- **Status:** ⚠️ Blocked
- **Related pages:** all service pages, [sitemap.xml](../sitemap.xml), [vercel.json](../vercel.json)
- **Evidence:** Current slugs are `geo-aeo.html`, `seo.html`,
  `paid-search-social.html`, `social-media.html`.
- **Notes:** Blocked on the keyword research the source repeatedly refers
  to ("add previous keywords research insights done") but does not
  include. **This is the highest-risk item in Iteration 3:** renaming live
  URLs breaks existing inbound links and search rankings unless 301
  redirects are added in `vercel.json` at the same time. Do not ship slug
  changes without redirects.

---

## 5. Open Items — Consolidated

Suggested delivery order. Grouping related IDs into one Pull Request
avoids repeatedly reworking the same markup.

### Batch A — Medsir (highest value, unblocks the most)

| ID | Item | Pages |
|---|---|---|
| CS-04 | Medsir case study section (`#case-medsir`) | results.html |
| HP-07 | Replace Uptoled KPI block with Medsir | index.html, results.html |
| HP-08 | Reorder KPI blocks: Indeed, Microsoft, Medsir | index.html, results.html |
| HP-09 | Case-study link font size and wording | styles.css |
| HP-10 | Bold client names in KPI descriptions | index.html |
| CS-01 | Case Studies KPI row | results.html |

### Batch B — Service page KPI copy

| ID | Item | Pages |
|---|---|---|
| GEO-03 | "Our GEO/AEO Optimization" supporting copy | geo-aeo.html |
| SEO-02 | "Our SEO/GEO Optimization" supporting copy | seo.html |
| SEO-03 | Move client results block below the hero | seo.html |

### Batch C — Paid Ads page

| ID | Item |
|---|---|
| PAID-01 … PAID-07 | Full page rebuild, including the Medsir client-results block |

### Batch D — Social Media page

| ID | Item |
|---|---|
| SOC-01 … SOC-06 | Full page rebuild |

### Batch E — SEO optimization

| ID | Item |
|---|---|
| META-01, META-02 | Meta titles and descriptions across all 9 pages |
| META-03 | URL slugs — **requires 301 redirects** |

### Awaiting input from Arthur

| ID | Blocked on |
|---|---|
| HP-01 | Decision: keep the hero carousel, or switch to a plain background |
| SEO-09 | LinkedIn photos (with permission) and landscape-layout confirmation |
| CS-05 | Case study screenshots, including the Gemini citation capture |
| META-03 | The keyword research referenced but not included in the source |

---

## 6. Ambiguities and Decisions Needed

**6.1 — Uptoled figure conflict.** The Homepage section lists "-30% Cost
Per Lead with Paid ads · Uptoled" in its bullet list, while the amended
text says "Change the block dedicated to Uptoled by -40% [...] Medsir".
*Resolution:* the amendment is authoritative — the block becomes −40%
Medsir. Recorded under HP-07.

**6.2 — Case study link wording.** The source uses three labels: "Read our
case study ->", "Review our case study" and "Read the case study". The
site currently has "Read the case study" on the homepage and "Review our
case study →" on the service pages. *Recommendation:* standardise on
"Review our case study →" everywhere. Needs confirmation.

**6.3 — Microsoft trial conversion figure.** The SEO page KPI specifies
"+5% conversion of organic visits to trial download" (SEO-07), while the
Microsoft case study specifies "+ 3%" (CS-03) — and the Jaroslav Pavlik
testimonial also says 3%. Both figures are live on the site today.
*Needs a decision:* which is correct? Two different numbers for the same
metric is a credibility risk on a page built to demonstrate rigour.

**6.4 — Hero background.** HP-01 is conditional and cannot be actioned as
written. *Needs a decision:* keep the current carousel, or replace it with
a plain background as the fallback instruction specifies.

**6.5 — Service link order on the homepage hero.** The hero's service
links run SEO first, while the main nav was reordered to lead with
GEO / AEO (HP-03). *Recommendation:* align the hero links to the nav
order. Low risk, needs confirmation.

**6.6 — Homepage service card truncations.** HP-11 contains two "remove
what is after" instructions that were not applied literally. The current
copy reads as finished prose. *Assumed intentional* — flag if not.

**6.7 — Netlify URL in the source.** The SEO Optimization section asks for
analysis of `https://experts-marketing.netlify.app`. Production is now
Vercel at `www.expertsmarketing.com`. *Resolution:* the Netlify reference
is stale; apply META-01 and META-02 to the live Vercel site.

**6.8 — Missing "Section 7" in the source.** The Homepage review jumps
from "6. Social Proof (Testimonials)" to "8. AI VISIBILITY REVIEW". No
section 7 exists in the source document. Recorded so future readers do not
assume a page is missing from the extraction.

---

## 7. Change Log

| Date | Change | PR |
|---|---|---|
| 2026-08-07 | Document created from the Iteration 3 PDF; full status audit against `main` @ `b7d9f88` | (this PR) |

### Iteration 3 work delivered before this document existed

| Date | Item | IDs | PR |
|---|---|---|---|
| 2026-08-06 | Standardize header nav across all pages, GEO/AEO before SEO | HP-03 | #11 |
| 2026-08-06 | Increase eyebrow label font size sitewide | HP-04 | #12 |
| 2026-08-07 | Replace default bullets with blue arrow markers | GEO-07, SEO-06 | #15 |
