# schuckdata.com

Marketing website for **Schuck Data**, a data consultancy specializing in dashboards, information pipelines, and data quality.

Live site: https://schuckdata.com (custom domain) / https://schuck-data.github.io (canonical GitHub Pages URL)

## Stack

- Hand-written HTML and CSS
- **Jekyll** for shared layout/header/footer across pages — runs automatically on GitHub Pages, no local build tooling required for editing
- Hosted on **GitHub Pages** (free, static-only)
- Repo named `schuck-data.github.io` so GitHub serves it as a [user site](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#types-of-github-pages-sites)
- Custom domain `schuckdata.com` configured via DNS at Namecheap

## Project structure

```
schuckdata/
├── _config.yml         # Jekyll site config
├── _layouts/
│   └── default.html    # Shared page wrapper (head, header, footer, scripts)
├── _includes/
│   ├── header.html     # Site header (logo + theme toggle)
│   └── footer.html     # Site footer (copyright + contact)
├── index.html          # Home page (Jekyll-processed: frontmatter + content)
├── styles.css          # All site styles
├── CNAME               # Custom domain for GitHub Pages
├── README.md           # This file
├── LICENSE             # All rights reserved (see License section)
├── .gitignore
├── docs/
│   └── decisions.md    # Running log of design/architecture decisions
└── mockups/            # Workshop reference pages (colors, typography)
```

## Local development

Editing content and styles: open files in any editor. No build step required for that.

Previewing locally with shared chrome rendered: you'd need Ruby + Jekyll installed (`gem install bundler jekyll`, then `bundle exec jekyll serve`). In practice, the project owner reviews changes on the deployed site after pushing — local Jekyll setup is optional.

GitHub builds the Jekyll site automatically on every push to `main`. Build status is visible under the **Actions** tab in the GitHub repo.

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

## License

© 2026 Schuck Data (DS89 Holdco LLC). **All rights reserved** — see [`LICENSE`](LICENSE). The entire repository, including the source code, the "Schuck Data" name and logo (trademarks), and the original artwork in `/ARTMATH/`, is proprietary. The repo is public only so the site can be served from GitHub Pages; publishing it grants no reuse rights. For permission, contact dakota@schuckdata.com.

## Contact

Dakota Schuck — dakota@schuckdata.com
