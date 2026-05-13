# Design & Architecture Decisions

A running log of meaningful choices made while building schuckdata.com. New entries go at the top. Each entry: what was decided, why, and what alternatives were considered.

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
