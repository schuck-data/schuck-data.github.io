# Design & Architecture Decisions

A running log of meaningful choices made while building schuckdata.com. New entries go at the top. Each entry: what was decided, why, and what alternatives were considered.

---

## 2026-05-21 — Landing redesign: rotating taglines, two-branch menu, logo, backstage

**Decision:** Reworked the homepage into an intent-based funnel while keeping the original navy/grid/glass theme and Inter type. The hero's five rotating questions were replaced by three rotating taglines (Get to the core of your data → Clean insights from messy information → Automation. Analytics. Answers.) using a lateral slide + fade with a 4.5s hold. Below the hero, a "We'll meet you where you are" section presents two expanding glass cards — Catching Up and Getting Ahead — each revealing three service options on first click. Option copy is verbatim from the Specs document. A "Skip the Menu, Talk to a Person" link is the escape hatch. Added a credential line ("Founded by a former U.S. Census Bureau statistician") with an Old Glory red/blue checkmark, the white logo lockup in the header (replacing the placeholder mark + text), and a footer copyright of "© 2026 Schuck Data — a DS89 Holdco LLC company".

**Interim contact:** menu options and the skip link point to `mailto:` links with a pre-filled subject, pending a Google Form that will pre-fill the selected service and ask a Time/Budget constraint.

**Backstage:** a subtle centered em-dash below the footer links to `/backstage/`, a hub for non-client-facing tools — a navy-restyled QR generator at `/QR/`, the QR redirect library (`/QRlibrary/SVSD1`, `/SVSD2` meta-refresh files → the SVSD Facebook page/group), and a planned Artmath gallery.

**Why:** The owner wanted the page to meet visitors where they are by intent, with progressive disclosure, while preserving the established theme. Copy is sourced from the Specs document, not code-example placeholders.

**Open:** Google Form wiring; Artmath gallery (needs the betterward.com/pnm reference + art assets); static QR images for the SVSD paths (generate via the new /QR tool — no QR library available in the build sandbox).

---

## 2026-05-13 — Full overhaul: navy-everywhere backdrop, glass surfaces, rotating hero questions

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

**Decision:** Page background is a neutral light grey (`#F4F4F5`) in light mode and near-black (`#0A0C10`) in dark mode. The header and main content sit inside white (or dark-elevated) rounded "tiles" with subtle borders and shadow. Footer sits directly on the page background without a tile.

**Why:** This pattern reads as "calm, structured, modern" — used by Linear, Vercel, Stripe Dashboard, Cal.com. Tiles give content visible boundaries without heavy hairlines, and the grey background prevents the page from feeling like an unstructured wall of text. Aligns with the design references workshopped earlier (Linear / Vercel / Plausible).

---

## 2026-05-13 — Dark mode palette switched to Electric Cobalt

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
