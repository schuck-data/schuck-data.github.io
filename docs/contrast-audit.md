# Color Contrast Audit — schuckdata.com

*Date: 2026-05-26 · Standard: WCAG 2.1 Level AA (the bar Section 508 incorporates by reference) · **Audit only — no CSS was changed.***

## What was checked and how

Every text and UI color token in `styles.css` (plus the inline styles on the QR and Artmath pages) was measured against the background it actually renders on. Contrast ratios use the WCAG relative-luminance formula.

Two things make this site's math non-obvious, and both were handled conservatively (worst case):

1. **The background is a gradient, not a flat color** — `#15293f → #1e3a5f`, plus a faint light-blue radial glow in the top-right corner. Lighter background = lower contrast for white text, so every white-text result below is measured against the **lightest** point: the glow corner, effective `rgb(42, 72, 111)`.
2. **Most text is semi-transparent white** (`rgba(255,255,255, α)`) and **sits on glass cards** (a white film at 4–8% over the navy). The text is composited onto the lightest realistic surface before measuring — again, the harshest case.

WCAG thresholds applied: **4.5:1** for normal text, **3:1** for large text (≥24px, or ≥18.66px bold) and for non-text UI components (1.4.11).

## Verdict

The core reading experience passes AA. **Three text tokens fail AA**, and all three trace back to deliberately "quiet" low-opacity styling. The most visible failure is the **footer**, which is client-facing on every page.

## Results

| Token / usage | Renders on | Ratio | Normal-text AA (4.5) | Notes |
|---|---|---|---|---|
| `--text` `#fff` — H1, headings, strong labels | navy / glass | **9.33 / 9.07** | ✅ (also AAA) | Excellent |
| `--text-muted` `white .72` — body prose, nav, blurbs | navy / glass | **5.74 / 5.62** | ✅ AA (AAA ✗) | Fine for 508 |
| `--accent` `#93c5fd` — links, eyebrows, numbers | navy / glass | **5.18 / 5.03** | ✅ AA (AAA ✗) | Fine; even at small sizes |
| `--accent-hover` `#bfdbfe` | navy / glass | **6.57 / 6.39** | ✅ AA | — |
| QR "on" segment label `#1e3a5f` on `#93c5fd` | accent btn | **6.38** | ✅ AA | — |
| QR printed caption `#000` on `#fff` | white card | **21.0** | ✅ | — |
| **`--text-subtle` `white .55`** — placeholder badges, QR labels/hints/footer text | navy / glass | **4.07 / 4.01** | ❌ **FAIL** | Small text; just short of AA |
| **`--text-faint` `white .40`** — footer copyright + email link, backstage "—" | navy / glass | **2.90 / 2.87** | ❌ **FAIL** | Client-facing footer, every page |
| **`.qr-empty` `#9aa3b2`** on white card — "Enter a URL" placeholder | white card | **2.54** | ❌ **FAIL** | QR page only (backstage) |

### Non-text UI (WCAG 1.4.11, needs 3:1)

| Token | Ratio on navy | 3:1 | Notes |
|---|---|---|---|
| Focus ring (`--accent`) | **6.38** | ✅ | Focus indicators are compliant — good |
| `--border` `white .10` | 1.35 | ❌ | Decorative card edges — likely exempt (see below) |
| `--border-strong` `white .18` | 1.72 | ❌ | Used on **QR form inputs** — borderline relevant |
| `--border-hover` `white .28` | 2.31 | ❌ | Decorative hover state |

## The three failures, with exact fixes (for later — not yet applied)

**1. Footer text — `--text-faint`, 2.90:1 → needs 4.5:1. (Highest priority: client-facing, every page.)**
Raising the alpha from `0.40` to **`0.62`** clears AA (4.71:1); `0.65` gives a little headroom (5.01:1). This affects `.site-footer`, its links, and the backstage "—". The footer is intentionally quiet, but at 0.40 the copyright and email link are hard to read.

**2. `--text-subtle`, 4.07:1 → needs 4.5:1.**
Raising `0.55` to **`0.62`** clears AA. Used by `.placeholder-note`, and across the QR tool (`label`, `.qr-hint`, `.tag`, `.qr-foot`). Many of these live on backstage/noindex pages, but it's a site-wide token so one change fixes all of them.

**3. `.qr-empty` placeholder — `#9aa3b2` on white, 2.54:1.**
Darken to **`#767676`** (4.54:1) — the standard "lightest accessible grey on white." QR page only.

**Optional (1.4.11):** if you want the **QR form input** borders to formally meet 3:1, `--border-strong` would need ~`0.375` alpha on navy. The glass *card* borders (`--border`, `--border-hover`) are decorative — the cards are identified by their fill, blur, and content, so they're reasonably treated as exempt. I'd only touch the input borders.

## Caveats

- **The hero canvas animation** draws faint light-blue dots/lines behind page text. It's low-opacity and masked, so it doesn't materially change the ratios above, but strictly speaking text contrast over an animated background isn't a fixed value. Honoring `prefers-reduced-motion` (already done) and the low opacity keep this a non-issue in practice.
- Ratios are computed against the **worst-case** background. On the darker parts of the gradient, every value above is *better* than shown.
- Nothing here was changed in CSS. If you want, the three fixes are three one-line edits to the `:root` variables plus one hex on `.qr-empty`.
