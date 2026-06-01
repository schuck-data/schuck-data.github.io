# Design & Architecture Decisions

A running log of meaningful choices made while building schuckdata.com. New entries go at the top. Each entry: what was decided, why, and what alternatives were considered.

---

> **Note on this log — read first.** This file was started by **previous builders** of the site. The dated entries below are a point-in-time historical record of decisions and their rationale. **They are NOT current instructions, and much of the older content is out of date.** Read them as "why we got here," not "how things are now." For what is actually true today, see **Current State** directly below. Convention going forward: when a decision changes, add a new dated entry at the top and mark the superseded entry — don't rewrite history.

---

## Current State (kept up to date)

*Last updated: 2026-06-01*

- **Hosting:** GitHub Pages from the `schuck-data.github.io` repo; custom domain `schuckdata.com` via Namecheap DNS. Auto-deploys on push to `main`.
- **Engine:** Jekyll — shared `_layouts/default.html` with `_includes/header.html` and `_includes/footer.html`.
- **Theme:** A single fixed **medium-navy** presentation (faint grid overlay + glass-morphic surfaces). **No light/dark toggle** — it was removed 2026-05-21.
- **Type:** Inter (Google Fonts), with monospace accents for eyebrows/labels.
- **Public pages:** `/` (home), `/services/`, `/founder/`, `/careers/`, `/contact/`. Header nav: Services · Founder · Careers · Contact. **About and Work were cut 2026-06-01.** **Routing convention (2026-06-01): lowercase is the norm; UPPERCASE is reserved for QR material only** (`/QR/`, `/QRLIBRARY/*`). All other routes (incl. `/sitemap/`, `/backstage/`, `/artmath/`) are lowercase. Each moved page sets a `permalink:` so it **builds at the lowercase URL regardless of its (still-capitalized) source folder name** — this is what makes the links resolve without any folder rename. Old caps paths redirect via `jekyll-redirect-from` (`redirect_from:` front matter). The source-folder case-rename is now **optional cosmetic cleanup** (see the 2026-06-01 entry), not required for the site to work.
- **Home hero:** the brand wordmark lives in the header nav bar (all pages, incl. home — no separate hero logo). The hero leads with a mono eyebrow ("Dashboards. Automation. Analytics."), the H1 "Data consultancy that makes sense.", a rotating tagline sub-line, and the Census-Bureau credential. Below it, the "What's your current need?" section has two expanding cards — "Something needs fixing." / "Looking to get ahead." — whose every option and the skip link point to `/CONTACT/`. (Social card title is set separately via `social_title`.)
- **Contact:** leads with the **embedded** client inquiry Google Form (iframe), with direct email/call/text kept below as a fallback (Forms can be blocked on gov networks) plus an "open in new tab" escape hatch. Live form: `forms.gle/fX1kLua6dekHETae9`.
- **Careers:** simplified to email-only (2026-06-01) — directs applicants to email `dakota@schuckdata.com` with a resume and any relevant skills/interests. The subcontractor Google Form was removed.
- **Content:** Services and Founder now carry **real copy** (2026-06-01). Founder = blended bio (Kelvin epigraph + narrative with a few concrete specifics: Census MAF/TIGER quality work, 2021 Texas vaccine processing, federal dashboards). Services = full seven-stage pipeline + offerings (incl. AI readiness). No political content.
- **Brand assets:** SD monogram favicon (`favicon.svg` + `favicon-32/16.png` + `apple-touch-icon.png` + `site.webmanifest`) and `og-image.png` for link previews. Logos in `/images/` (white version used on navy; the white variants were regenerated from the black master on 2026-05-26).
- **Licensing:** whole-site **All Rights Reserved** (see root `LICENSE`); the name/logo are trademarks and `/ARTMATH/` is reserved. Not open-source.
- **Backstage:** `/backstage/`, `/QR/`, and `/QRLIBRARY/*` are kept out of search via per-page `noindex` (deliberately NOT a robots `Disallow`, which would both list the paths and block crawlers from reading the noindex). They stay reachable by direct URL; the footer em-dash link is `rel="nofollow"`.
- **Mockups:** `/mockups/*` are retained in the repo as workshop reference but excluded from the Jekyll build (no longer published).

---

## 2026-06-01 — Launch pass: lowercase routes, cut About/Work, real Services & Founder copy, embedded form

**Decision:** Workshop-then-implement session to finish the public site.

- **Routing convention flipped to lowercase.** UPPERCASE is now reserved *only* for QR material (`/QR/`, `/QRLIBRARY/SVSD1`, `/QRLIBRARY/SVSD2`). Every other route is lowercase, and all internal links/nav/sitemap/footer were repointed. Chosen over Jekyll-permalink trickery (owner wanted real lowercase folders, cleaner repo) and over a no-redirect rename (keep old links alive).
- **`permalink:` makes lowercase URLs work without the rename.** Each moved page (`services`, `founder`, `careers`, `contact`, `sitemap`, `backstage`, `artmath`) sets `permalink: /lowercase/` in its front matter, so Jekyll builds it at the lowercase URL even though the source folder is still capitalized. *(Added 2026-06-01 after the first push 404'd: links had been repointed to lowercase, but with no permalink and no rename the pages were still building at the old caps URLs.)* This also frees the caps path for the redirect stub (no collision), so the redirects work with or without the folder rename.
- **Redirects via `jekyll-redirect-from`.** Added the plugin to `_config.yml` and a `redirect_from:` block to each moved page naming its old caps path, so pre-existing links to `/SERVICES/`, `/CONTACT/`, etc. don't 404. Cut pages redirect too: `/ABOUT/` → `/founder/`, `/WORK/` → `/services/`. *This plugin is GitHub Pages-supported but could not be tested locally (no local build); if the build ever misbehaves, removing the `plugins:` line disables redirects without affecting the main pages.*
- **Filesystem constraint that shaped the approach.** The repo is on a case-insensitive Windows volume, so `SERVICES/` and `services/` are the same folder — you cannot keep a caps redirect stub next to a lowercase page in source, and a case-only rename can't be done through the mounted volume. The redirect stubs are therefore generated at *build* time (Linux, case-sensitive) by the plugin, and the `permalink:` handles the lowercase URLs at build time too. **The folder rename below is now OPTIONAL cosmetic cleanup** (to make the source folders match the URLs) — the site works without it. If you do want tidy folders, run this on the host in the repo root:

  ```
  git mv SERVICES SERVICES_tmp && git mv SERVICES_tmp services
  git mv FOUNDER FOUNDER_tmp && git mv FOUNDER_tmp founder
  git mv CAREERS CAREERS_tmp && git mv CAREERS_tmp careers
  git mv CONTACT CONTACT_tmp && git mv CONTACT_tmp contact
  git mv SITEMAP SITEMAP_tmp && git mv SITEMAP_tmp sitemap
  git mv BACKSTAGE BACKSTAGE_tmp && git mv BACKSTAGE_tmp backstage
  git mv ARTMATH ARTMATH_tmp && git mv ARTMATH_tmp artmath
  git add -A
  git commit -m "Lowercase routes (QR stays caps); cut About/Work; real Services/Founder copy; embed inquiry form; Careers to email"
  git push
  ```

  The two-step `_tmp` rename is needed because git on Windows is case-insensitive by default. (With the `permalink:` in place the caps page no longer occupies `/SERVICES/`, so there's no longer a redirect collision to worry about — the rename is safe to do whenever, or never.) If you rename, you can delete the now-redundant `permalink:` lines, but leaving them is harmless.
- **About and Work pages cut.** `ABOUT/` and `WORK/` deleted from the working tree (`git add -A` records the removal). Removed from nav and sitemap; old URLs redirect (above).
- **Services — real copy.** Replaced lorem with the full seven-stage information pipeline (sampling → presentation) as a styled stage list, plus offering cards (Data Health Reports, Report Automation & Dashboards, Statistical Modeling & Forecasting, **AI Readiness** — new selling point, Data Department Development, Retainer Packages, Data Sculptures). Page-scoped CSS only; no change to global `styles.css`.
- **Founder — real bio.** Replaced lorem with a Lord Kelvin epigraph + a narrative bio seasoned with a few concrete specifics (Census MAF/TIGER quality work incl. PCA and block-canvassing risk measures, 2021 Texas vaccine applicant processing, federal data dashboards). Tone is an about-section, not a resume.
- **Contact — embedded form.** Swapped the placeholder link-out for the real client inquiry form **embedded** as an iframe (`forms.gle/fX1kLua6dekHETae9`), keeping email/call/text fallback + an "open in new tab" link below for locked-down networks.
- **Careers — email-only.** Removed the (placeholder) subcontractor form; the page now directs applicants to email a resume + relevant skills/interests.

**Open:** run the git rename script above and push; rebuild `og-image.png` from the updated logo (still pending from 2026-05-26); the root `LICENSE`/`README` still reference the artwork at `/ARTMATH/` — update to `/artmath/` when convenient (build-excluded, legal doc, so left for owner to edit).

---

## 2026-05-26 — White logo fix, whole-site All Rights Reserved, contrast AA fixes, Google Form paths

**Decision:** A working session covering five items (full write-ups added under `docs/`).

- **White logo regenerated from the black master.** The shipped `schuck-data-white*.png` were a slightly *eroded* derivative of the current black logo — the thin connecting lines of the chart motif had been dropped, so it read as loose dots rather than a connected trend line. Regenerated all three white variants directly from `schuck-data-black.png` (recolor RGB→white, keep the master's alpha exactly). Alpha is now pixel-identical to the black master; the connected rising-chart is restored. Then **thickened the chart's connector lines** (~4.4px → 10px) across the black, white, and color masters: the chart motif is a single connected component (the peak dot is the logo's highest point), so it isolates cleanly from all 10 letters; within it, the 3 thin segments between the 4 dots were redrawn at 4× resolution between the detected dot centers and composited under the dots — dots and lettering untouched, header crop dimensions unchanged. **`og-image.png` (social card) still shows the older thinner lines** — it's a separate baked composite and is pending a rebuild from the updated logo.
- **Licensing → whole-site All Rights Reserved.** Replaced the repo-wide MIT license (which would have granted anyone the right to copy/sell the logo and the Artmath artwork) with a single All-Rights-Reserved notice that also names the "Schuck Data" name/logo as trademarks and the `/ARTMATH/` artwork as reserved. Owner of record: Schuck Data (DS89 Holdco LLC), matching the footer. The considered alternative (keep code MIT, carve out only brand + art) and rationale are in `docs/licensing-recommendation.md`. Whole-site ARR was chosen as the simpler, safer option — no bundled third-party code to conflict with (QR lib + Inter font load from CDNs at runtime).
- **Contrast AA fixes (Section 508).** From `docs/contrast-audit.md`: `--text-faint` 0.40→0.62 and `--text-subtle` 0.55→0.65 (both now ≥4.5:1 on the worst-case navy; hierarchy below `--text-muted` 0.72 preserved), and the QR placeholder `.qr-empty` `#9aa3b2`→`#767676` (4.54:1 on white). Opaque text, muted text, the accent, and the focus ring already passed AA.
- **Client & subcontractor paths.** Contact and Careers now lead with a Google Form CTA (link-out), with direct email/call/text kept as a fallback — the fallback matters because Google Forms is blocked on some government networks. Form URLs are placeholders (`REPLACE-WITH-…`) pending the real forms; field specs in `docs/forms-setup.md`.
- **Grayware (analysis only — no code changed).** `docs/grayware-review.md` ranks the likely web-filter triggers (most probably a new/uncategorized domain plus the QR `QRLIBRARY` meta-refresh redirects to Facebook; secondarily the perpetual `requestAnimationFrame` canvas animations and the no-SRI CDN script) and includes a vendor re-categorization checklist.

**Open:** create the two Google Forms and replace the placeholder URLs; decide whether to implement the grayware code mitigations (SRI on the QR CDN script; a meta CSP; gating the Artmath canvases on `prefers-reduced-motion` + visibility; a visible interstitial instead of the instant Facebook meta-refresh). Note: a corrupted `.git/index` was observed this session — rebuild it on the host with `git reset` (working-tree files are intact).

---

## 2026-05-21 — Site expansion: single navy, fixed H1 + tagline, multi-page, interim contact, favicon, backstage noindex, cleanup

**Decision:** A round of changes following a fresh design review.

- **Removed the light/dark theme toggle** and locked the site to one **medium-navy** palette. The toggle only switched between two navies (not a true light mode), so the sun/moon icon over-promised. Deleted the toggle button, both theme scripts, and all `[data-theme]` / `prefers-color-scheme` CSS branches.
- **Hero rework for SEO + clarity:** the rotating taglines were demoted off the `<h1>` (they were evocative but keyword-empty). The H1 is now a single, stable, keyword-bearing line — "Data consultancy for teams who need clear, clean, and actionable data." The three taglines now animate as a sub-line beneath it (same slide+fade JS; the element kept the class `rotating-headline` but is a `<p>`, not the `<h1>`).
- **Logo usage:** the header wordmark was rendering at ~32px (too small for a wide lockup). Enlarged it, and added the white logo as a large brand moment in the hero. On the homepage the header wordmark is hidden (`hide_header_logo`) so the hero logo isn't duplicated directly above it; the header there is centered nav.
- **Navigation + pages:** added header nav and scaffolded `/ABOUT/`, `/FOUNDER/`, `/SERVICES/`, `/WORK/`. Content is placeholder lorem except confirmed facts.
- **Interim contact:** built `/CONTACT/` (under construction; direct email/call/text) and repointed all six menu options + the skip link to it, replacing the `mailto:` links. The expanding two-branch menu was kept; the a/b/c option lettering was removed.
- **Favicon + meta:** created an SD monogram favicon set + `og-image.png`, and added favicon links, canonical, and Open Graph / Twitter tags to the shared head. Corrected `theme-color` to the medium navy.
- **Backstage discoverability:** `noindex` on BACKSTAGE/QR, `rel="nofollow"` on the footer link, and a permissive `robots.txt` that intentionally does not enumerate the backstage paths.
- **Cleanup:** removed dead CSS (`.cta*`, `.logo-mark`, `.hero-actions`), consolidated the duplicated/overridden `.page`, `.page-main`, `.hero`, and `.rotating-headline` rules into single canonical definitions (deleting the old "compaction" override block), and excluded `/mockups/` from the build (`styles.css` 673 → 583 lines).

**Why:** The review found the design container was strong but the site was thin (little content, mailto-only conversion), the toggle was confusing, the H1 wasn't doing SEO work, the logo was underused, and the CSS had accumulated three redesigns' worth of overrides.

**Open:** real copy for About/Founder/Services/Work; case studies; the inquiry form to replace the interim contact page; confirm/verify rendered spacing and logo sizing on the deployed site.

---

## 2026-05-21 — Landing redesign: rotating taglines, two-branch menu, logo, backstage

**Decision:** Reworked the homepage into an intent-based funnel while keeping the original navy/grid/glass theme and Inter type. The hero's five rotating questions were replaced by three rotating taglines (Get to the core of your data → Clean insights from messy information → Automation. Analytics. Answers.) using a lateral slide + fade with a 4.5s hold. Below the hero, a "We'll meet you where you are" section presents two expanding glass cards — Catching Up and Getting Ahead — each revealing three service options on first click. Option copy is verbatim from the Specs document. A "Skip the Menu, Talk to a Person" link is the escape hatch. Added a credential line ("Founded by a former U.S. Census Bureau statistician") with an Old Glory red/blue checkmark, the white logo lockup in the header (replacing the placeholder mark + text), and a footer copyright of "© 2026 Schuck Data — a DS89 Holdco LLC company".

**Interim contact:** menu options and the skip link point to `mailto:` links with a pre-filled subject, pending a Google Form that will pre-fill the selected service and ask a Time/Budget constraint.

**Backstage:** a subtle centered em-dash below the footer links to `/BACKSTAGE/`, a hub for non-client-facing tools — a navy-restyled QR generator at `/QR/`, the QR redirect library (`/QRLIBRARY/SVSD1`, `/SVSD2` meta-refresh files → the SVSD Facebook page/group), and a planned Artmath gallery.

**Paths are UPPERCASE:** all public routes use uppercase (`/QR/`, `/BACKSTAGE/`, `/QRLIBRARY/SVSD1`, `/SVSD2`). Rationale: GitHub Pages serves paths case-sensitively and offers no server-side redirect, so the site commits to one canonical case. Uppercase was chosen so the QR-encoded URLs (e.g. `HTTPS://SCHUCKDATA.COM/QRLIBRARY/SVSD1`) stay entirely within the QR *alphanumeric* charset (0-9 A-Z space $%*+-./:), which encodes more compactly than byte mode and yields a denser, more reliably scannable code. The folder renames (`backstage`→`BACKSTAGE`, `QRlibrary`→`QRLIBRARY`) must be done with `git mv` so Git records the case change for the Linux build server.

**QR caption:** the QR generator now prints the exact encoded string along the bottom edge of every code (SVG and PNG), with extra bottom margin and a font size that auto-fits the width, so a printed code is self-labeling.

**Why:** The owner wanted the page to meet visitors where they are by intent, with progressive disclosure, while preserving the established theme. Copy is sourced from the Specs document, not code-example placeholders.

**Open:** Google Form wiring; Artmath gallery (needs the betterward.com/pnm reference + art assets); static QR images for the SVSD paths (generate via the new /QR tool — no QR library available in the build sandbox).

---

## 2026-05-13 — Full overhaul: navy-everywhere backdrop, glass surfaces, rotating hero questions

> **Status:** Partially superseded (2026-05-21). The navy backdrop, grid, and glass surfaces are still current; the light/dark toggle was removed and the rotating questions were moved off the `<h1>` into a sub-line.

**Decision:** Replace the previous "grey page bg with white content tiles" architecture entirely. The whole site now sits on a single continuous deep-navy backdrop with a fading grid pattern overlay. Content surfaces (header, service cards) are glass-morphic — translucent white at low opacity with backdrop-blur — so the grid faintly shows through. The brand is the navy; the theme toggle now switches between "medium navy" (formerly light mode) and "deeper near-black navy" (formerly dark mode), but the aesthetic is consistent across both.

**Hero centerpiece:** The H1 rotates through five attention-grabbing questions about data trust, freshness, accuracy, AI-readiness, and visualization quality. Each question is displayed for 4.5 seconds with a smooth 600ms opacity+translate fade transition. Auto-rotation pauses on hover, focus, and when the browser tab is hidden. Honors `prefers-reduced-motion` (shows only the first question, no rotation). All questions exist in the DOM as `<span>` children of the `<h1>` so screen readers and search engines see all of them; `aria-live="polite"` announces changes for screen reader users.

**Why:** Previous "white tile on grey" architecture was reading as bloggy and quiet. The site owner explicitly asked for a "tech-experts" register, with a single continuous backdrop instead of stacked tiles. The rotating questions provide visual interest, kinetic energy, and immediately communicate the *kinds of problems* Schuck Data solves — turning what was a static brand statement into a sequence of probing questions the visitor can identify with.

**Implementation notes:**
- Body background: fixed-attachment linear gradient (navy) plus a faint radial accent glow in the top-right corner. A pseudo-element draws the 48×48px grid overlay, fixed to the viewport and masked with a `linear-gradient` so it fades out around 75% down the viewport.
- `theme-color` meta tag set to match the deep navy so mobile browsers tint the URL bar appropriately.
- Glass cards use `backdrop-filter: blur(10-12px) saturate(180%)` plus low-opacity white fills and borders. Hover state lifts the card 2px, lightens the surface, brightens the border, and reveals a subtle top-left radial highlight via `::after`.
- CTAs adapted: primary is white-on-navy (highest contrast on the dark backdrop); secondary is outlined.
- Theme toggle, services hover, smooth scroll, and reduced-motion all preserved from prior iteration.

**Trade-offs:**
- The light/dark toggle is less functionally distinct now (both modes are dark navy). Kept for user preference, but it's a "vibe" adjustment rather than a true light/dark flip.
- Glass-morphism requires `backdrop-filter` browser support — fine in all modern browsers; older browsers fall back to the solid translucent fill (still legible).
- Larger visual commitment than the previous tile-based design. Counterbalance: real positioning content workshop is now top priority.

**Alternatives considered:** Keep tile architecture and just darken the bg (rejected — wouldn't have addressed the "bloggy" critique); remove the theme toggle entirely (kept for now since the user chose to add it earlier).

---

## 2026-05-13 — Homepage design: "Bold Editorial" hero (Direction 3)

> **Status:** Superseded. The tile-based hero/services architecture described here was replaced by the navy overhaul and the 2026-05-21 hero rework.

**Decision:** The homepage uses a full-color deep-navy hero tile as the primary visual moment, followed by a white services tile below. The hero is theme-independent (always navy with white text) — a constant brand statement regardless of whether the rest of the page is in light or dark mode. Services section flips with the page theme.

**Implementation:**
- `<main>` is now a transparent section wrapper; individual page sections are their own tiles. Layout was refactored from "single content tile" to "stacked section tiles."
- Hero tile: linear gradient from `#15293F` to `#1E3A5F`, with a faint white grid pattern overlaying the top-right corner that fades out via a radial mask. Headline is large (clamp 2.25-4rem), 700 weight, tight letter-spacing. Eyebrow in light-blue (`#93C5FD`). Primary CTA is white-on-navy; secondary is outlined-on-navy.
- Services tile: standard white surface with three numbered cards in a responsive grid. Subtle hover state lifts cards and switches their border to the accent color.
- CSS variables for hero colors are intentionally outside the light/dark switching block — the hero is always navy.

**Why:** Of the four directions workshopped (Refined Minimal, Split with Data Graphic, Bold Editorial, Editorial Split), Direction 3 was the picked vibe. The bold hero gives the brand a memorable visual statement; the constant navy color across themes makes the site feel branded rather than "configurable." The risk — bold visuals demand strong content — is acknowledged: real positioning copy is the next priority, not deferred.

**Alternatives considered:** Refined Minimal (rejected — too quiet for the intended brand register); Split with Data Graphic (close runner-up — could be a future homepage variation); Editorial Split (rejected — too magazine-formal for a small consultancy).

**See:** `mockups/homepage-directions.html` retained in repo as the workshop reference.

---

## 2026-05-13 — Jekyll for shared chrome across pages

**Decision:** Use Jekyll (GitHub Pages' built-in static site engine) to share header/footer/layout across pages, rather than duplicating HTML or using runtime JavaScript includes.

**Implementation:**
- `_config.yml` — minimal Jekyll config (site title, description, exclude list)
- `_layouts/default.html` — page wrapper with `<head>`, header include, content slot, footer include, theme toggle script
- `_includes/header.html` — site header (logo + theme toggle)
- `_includes/footer.html` — site footer (copyright + contact)
- Each page has a small YAML frontmatter block specifying `layout: default` and optional `title` / `description`, then page-specific content

**Why:** With ~5 planned pages sharing the same header and footer, duplication would multiply maintenance cost; JS includes would hurt SEO (search engines don't reliably execute JavaScript for content) and cause a flash of unstyled content on load. Jekyll is built into GitHub Pages (zero configuration on the publishing side), requires no local build tooling for editing pages, and is the canonical solution for this exact problem.

**Trade-offs:**
- Pages have YAML frontmatter at the top instead of being raw HTML. Minor cognitive cost.
- Previewing locally requires Ruby + Jekyll installed. Mitigation: site owner reviews on the live URL anyway (no working preview panel in their environment).
- Migration away from Jekyll is straightforward — inline the includes back into pages, delete `_config.yml` and `_layouts/`, and you have plain HTML again.

**Alternatives considered:** Duplicate HTML (rejected — scales poorly); JS `fetch()` includes (rejected — SEO and UX cost); Astro / Eleventy (rejected — adds local build tooling, contradicts the hand-written-HTML decision).

---

## 2026-05-13 — Layout pattern: grey page background with white content tiles

> **Status:** Superseded. The grey-background / white-tile pattern was fully replaced by the single navy backdrop with glass surfaces.

**Decision:** Page background is a neutral light grey (`#F4F4F5`) in light mode and near-black (`#0A0C10`) in dark mode. The header and main content sit inside white (or dark-elevated) rounded "tiles" with subtle borders and shadow. Footer sits directly on the page background without a tile.

**Why:** This pattern reads as "calm, structured, modern" — used by Linear, Vercel, Stripe Dashboard, Cal.com. Tiles give content visible boundaries without heavy hairlines, and the grey background prevents the page from feeling like an unstructured wall of text. Aligns with the design references workshopped earlier (Linear / Vercel / Plausible).

---

## 2026-05-13 — Dark mode palette switched to Electric Cobalt

> **Status:** Superseded (2026-05-21). There is no longer a separate dark mode — the site uses a single medium-navy palette.

**Decision:** Light mode keeps the Deep Ink Blue palette (accent `#1E3A5F` on white). Dark mode swaps to Electric Cobalt values: background `#0A0C10`, surface `#14171F`, accent `#60A5FA`.

**Why:** Workshop-time decision was Deep Ink Blue across both modes. On reflection, the Electric Cobalt dark-mode accent (`#60A5FA`) reads more vibrantly against the deep cool-black surface than the lighter sky-blue (`#93C5FD`) read against pure black — it has more "presence" without being saturated. Light mode unchanged.

**Note:** The accent colors are different hues, but they share visual family (both are calm cool blues). The brand identity is anchored to Deep Ink Blue; the dark-mode shift is a render adjustment for the surface change, not a separate brand.

---

## 2026-05-13 — Typography: Inter

**Decision:** Use Inter (Google Fonts, variable weight) for all text — headings and body. Falls back to the system sans stack if the web font fails to load (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, ...`).

**Implementation:**
- Loaded via Google Fonts with `display=swap` so text renders immediately in the fallback stack, then swaps to Inter once downloaded — no FOIT (flash of invisible text).
- `font-feature-settings: "cv11", "ss01", "ss03"` enabled — Inter's stylistic variants for a more readable lowercase `l`, single-story `a`, and improved figure rendering.
- `<link rel="preconnect">` to `fonts.googleapis.com` and `fonts.gstatic.com` for faster initial load.

**Why:** Of the four options workshopped (System sans, Inter, IBM Plex, Fraunces+Inter), Inter is the most-used modern web typeface for B2B / SaaS for good reason: highly legible at all sizes, designed specifically for UI, supports useful OpenType features (tabular numerals, contextual alternates), and renders identically across operating systems. Lower brand-distinctiveness than IBM Plex or Fraunces, but the trade is "instantly familiar and easy to read" — which for a consultancy is on-strategy.

**Alternatives considered:** System sans (rejected — inconsistent across platforms); IBM Plex (close second — more character, slightly more technical register); Fraunces+Inter (rejected — serif headings are distinctive but lean formal-editorial, more identity commitment than is warranted at launch).

**See:** `mockups/typography.html` retained in repo as workshop reference.

---

## 2026-05-13 — Theme toggle (manual override of system preference)

> **Status:** Superseded (2026-05-21). The theme toggle was removed; the site is a single fixed navy theme.

**Decision:** Add a small fixed-position toggle button (top-right) that lets users manually switch between light and dark mode. Default behavior still respects the OS `prefers-color-scheme`; clicking the toggle saves an explicit override to `localStorage`.

**Why:** Some users prefer to lock the site to a specific mode independent of their system setting (e.g., reading in a dark office during daytime). Toggle gives that control without sacrificing the smart default.

**Implementation notes:**
- CSS theme variables switch on the `data-theme` attribute of `<html>` (`"light"` or `"dark"`).
- Inline `<script>` in `<head>` applies saved preference before first paint to avoid theme flash.
- A separate script at end of `<body>` wires up the click handler.
- Two-state toggle (light ↔ dark) — no "reset to system" option yet. Trade-off: simpler UX, but once toggled, the page no longer follows OS mode changes. Revisit if it becomes annoying.

**Alternatives considered:** Three-state cycle (system → light → dark) — rejected for now as less obvious. Could be added later without breaking existing user preferences.

---

## 2026-05-13 — Color palette: Deep Ink Blue, with light + dark mode support

> **Status:** Partially superseded (2026-05-21). The Deep Ink Blue family remains the brand, but light/dark mode support was dropped in favor of a single medium-navy presentation.

**Decision:** Adopt the "Deep Ink Blue" palette from the color workshop. Site supports both light and dark mode via `prefers-color-scheme`, with light as the primary brand presentation (used for screenshots, social previews, marketing materials).

| Token | Light mode | Dark mode |
|---|---|---|
| Background | `#FFFFFF` | `#0A0A0A` |
| Text | `#0A0A0A` | `#E5E5E5` |
| Accent (headings, buttons, links) | `#1E3A5F` | `#93C5FD` |

All combinations exceed WCAG 2.0 AAA contrast (the highest tier).

**Why:** Trustworthy and substantial — the "blue chip consultancy" register fits a data quality / decision-support firm better than energetic startup or experimental palettes. Of the four directions workshopped (Deep Ink, Electric Cobalt, Forest, Teal), this one is hardest to look unprofessional and translates well across screenshots, PDFs, and print. The lighter dark-mode variant (`#93C5FD`) reads almost luminous against pure black, giving the dark theme a distinct character without becoming a different brand.

**Alternatives considered:** Electric Cobalt (rejected — overused in B2B SaaS, would dilute differentiation); Forest Green (rejected — risks reading as fintech or sustainability brand); Teal (close second — more differentiated, but Deep Ink prioritizes trust over novelty, which is on-strategy for the consultancy positioning).

**See:** `mockups/colors.html` for the full workshop (kept in repo as reference).

---

## 2026-05-13 — Hosting on GitHub Pages with custom domain

**Decision:** Host on GitHub Pages from the `schuck-data.github.io` user-site repo, point `schuckdata.com` at it via Namecheap DNS.

**Why:** Free, reliable, built-in HTTPS, no vendor lock-in, fits a static informational site. The user-site repo pattern (vs. project site) is simpler for a primary domain and gives a cleaner canonical URL.

**Alternatives considered:** Netlify (would gain easy forms but adds a dependency); Cloudflare Pages (similar to GitHub Pages but separates DNS and hosting providers unnecessarily for now).

---

## 2026-05-13 — Hand-written HTML & CSS, no framework

> **Status:** Partially superseded. Still hand-written HTML/CSS with no build step locally, but Jekyll (next entry) was adopted for shared header/footer/layout.

**Decision:** Build the site as plain HTML and CSS files with no build step, no JavaScript framework, no static site generator.

**Why:** Site is small (5-ish pages), content changes infrequently, and the owner wants to understand every line. Zero build complexity means zero dependency upkeep and instant local previewing. If page count grows past ~10 or templating pain emerges, we can introduce Astro later without losing work.

**Alternatives considered:** Astro (good defaults, component reuse — overkill for current scope); Jekyll (GitHub Pages native, but Ruby tooling adds friction on Windows).

---

## 2026-05-13 — MIT License

**Decision:** Use MIT license for the repo.

**Why:** Standard for public marketing-site code, maximally permissive, no copyleft obligations. The website content (copy, brand) is separately the owner's intellectual property; the license covers the code/markup.

**Alternatives considered:** No license (would make code technically unreusable by anyone, including future contractors); CC-BY for content (could be added later if needed).

---

## 2026-05-13 — Section 508 / WCAG 2.0 AA accessibility target

**Decision:** Design for Section 508 compliance, which in practice means WCAG 2.0 AA: 4.5:1 contrast for normal text, 3:1 for large text and UI components.

**Why:** Reasonable baseline for a public-facing business site; signals professionalism for a data consultancy where clients may include government or compliance-sensitive orgs.

**Open question:** Final color palette pending workshop. Placeholder accent in `styles.css` is `#1e3a5f` (deep ink blue, ~9.7:1 against white) — not the final choice.
