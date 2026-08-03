# Experts Marketing — Static Website

A multi-page static site for Experts Marketing, a Barcelona-based AI-enhanced marketing agency.

## How to view it

This is a plain static site. You don't need a build step or a server — just open `index.html` in a browser.

```
experts-marketing/
├── index.html              # Homepage
├── about.html              # About / founder
├── contact.html            # Contact form + alt CTAs
├── results.html            # KPIs + case studies
├── services/
│   ├── seo.html
│   ├── geo-aeo.html
│   ├── paid-search-social.html
│   └── social-media.html
├── assets/
│   ├── styles.css          # Single design system
│   └── images/             # Logo, favicon, photos
└── README.md               # This file
```

## Deploying

Drop the whole folder into any static host:

- **Netlify** — drag the folder onto https://app.netlify.com/drop
- **Vercel** — `vercel deploy` from the folder
- **GitHub Pages** — push to a repo, enable Pages on the main branch
- **Cloudflare Pages** — connect a repo or upload the folder
- **Any web server** — copy to `/var/www/html` or your bucket of choice

All paths are relative, so it works from any subdirectory too.

## Editing

- Colors and typography live in `assets/styles.css` under `:root` (CSS custom properties).
- Page content is in the HTML files — no framework, no JS dependencies.
- The site loads Inter and Fraunces from Google Fonts; everything else ships in the folder.

## Browser support

Modern evergreen browsers (Chrome, Safari, Firefox, Edge). Uses CSS grid, custom properties, `aspect-ratio`, and `backdrop-filter`.
