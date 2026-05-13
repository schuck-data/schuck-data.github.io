# Design & Architecture Decisions

A running log of meaningful choices made while building schuckdata.com. New entries go at the top. Each entry: what was decided, why, and what alternatives were considered.

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
