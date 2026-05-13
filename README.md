# schuckdata.com

Marketing website for **Schuck Data**, a data consultancy specializing in dashboards, information pipelines, and data quality.

Live site: https://schuckdata.com (custom domain) / https://schuck-data.github.io (canonical GitHub Pages URL)

## Stack

- Hand-written HTML and CSS — no framework, no build step
- Hosted on **GitHub Pages** (free, static-only)
- Repo named `schuck-data.github.io` so GitHub serves it as a [user site](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#types-of-github-pages-sites)
- Custom domain `schuckdata.com` configured via DNS at Namecheap

## Project structure

```
schuckdata/
├── index.html          # Home page
├── styles.css          # All site styles
├── README.md           # This file
├── LICENSE             # MIT
├── .gitignore
└── docs/
    └── decisions.md    # Running log of design/architecture decisions
```

## Local development

No build step. Just open `index.html` in a browser, or use VS Code's "Live Server" extension for auto-reload on save.

## Deploying

Every push to `main` is auto-published by GitHub Pages within ~30-60 seconds. There is no separate deploy step.

To set up GitHub Pages for this repo (one-time, after first push):
1. Go to **Settings → Pages** in the GitHub repo
2. Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)**
4. Save

For the custom domain (one-time, after Pages is working):
1. Add a `CNAME` file at the repo root containing `schuckdata.com`
2. At Namecheap → Advanced DNS for `schuckdata.com`, add:
   - **A records** pointing `@` to GitHub Pages IPs: `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - **CNAME record** for `www` → `schuck-data.github.io`
3. In GitHub repo Settings → Pages, enter `schuckdata.com` as the custom domain and enable "Enforce HTTPS" once the cert provisions (can take up to 24h on first setup)

## Redirect domains

These five domains are owned and redirect 301 → `schuckdata.com` via Namecheap's URL Redirect Record feature (no hosting needed):
- schuckandcompany.com
- schuckcompany.com
- schuckdatasolutions.com
- shuckcompany.com
- shuckdata.com

## Design

See [`docs/decisions.md`](docs/decisions.md) for the running log of design and architecture decisions and their rationale.

## Contact

Dakota Schuck — dakota@schuckdata.com
