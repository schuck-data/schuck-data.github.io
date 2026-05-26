# Licensing & Ownership — Recommendation

*Date: 2026-05-26 · **Advisory.** Nothing has been changed; ready-to-apply drafts are at the bottom for your go-ahead.*

> Not legal advice — I'm not a lawyer. This is the practical, widely-used best-practice pattern for separating ownership layers in a public repo, plus the facts you'd need to make the call. For anything high-stakes (e.g., if the artwork becomes commercially valuable), have an IP attorney confirm.

## The problem you flagged, stated precisely

Your repo is **public** and currently carries a single top-level **MIT license** ("Copyright (c) 2026 Dakota Schuck"). MIT grants *everyone* the right to "use, copy, modify, merge, publish, distribute, sublicense, and/or sell" **the Software** — and "the Software" is undefined, so a reasonable reader takes it to mean *everything in the repository*: the code, the logo image files, **and** the Artmath artwork. That's the accidental give-away: as written, MIT lets a stranger take your logo or your generative art, modify it, and sell it, with only an attribution-notice obligation.

Two facts make the fix straightforward:

- **A public repo does not, by itself, grant reuse rights.** Posting code publicly only grants what GitHub's Terms of Service cover (others may *view and fork within GitHub*). Broad reuse rights come *only* from the license you choose. So the lever is entirely in your hands: scope the license.
- **Trademark is separate from copyright.** Neither MIT nor Creative Commons ever grants trademark rights — CC excludes them explicitly. But the *copyright* in your logo's image files **is** granted by an unscoped MIT. So the logo needs to be carved out of the **copyright** grant, and additionally noted as a trademark.

## The three ownership layers on this site

1. **Site code** — the HTML/CSS, Jekyll scaffolding, the in-browser QR generator. Generic, reusable web plumbing. Keeping this open (MIT) is fine and even good for your reputation as a data shop. Nothing here is worth protecting.
2. **Brand / trademark** — the "Schuck Data" name, the SCHUCK·DATA wordmark and logo (`/images/schuck-data-*.png`, `favicon.*`, `og-image.png`). These identify your business. They should be **All Rights Reserved** and labeled as trademarks.
3. **Original artwork** — the Artmath generative pieces (`/ARTMATH/`). This is the layer you specifically want to keep. Note it has two sub-parts: the *code that draws the art* and the *artwork itself as a creative work*. You can treat them differently if you want (e.g., code MIT, output reserved), but the cleaner story is to reserve the whole Artmath directory.

## Recommended approach

**Keep MIT for the code; carve brand and artwork out of it explicitly; mark them in the folders where they live.** This is the lightweight version of the industry-standard pattern (the rigorous version is the [REUSE/SPDX](https://reuse.software/faq/) per-file approach, which is overkill for a site this size — see "If you want to be rigorous" below).

Concretely, four small changes:

1. **`LICENSE`** — keep the MIT text verbatim, but add a short **scope preamble** above it that defines "the Software" as the source code and explicitly excludes the brand and the artwork. (Don't edit the MIT body itself — appending a scope note is unambiguous and keeps MIT intact.)
2. **`/images/LICENSE`** — a one-paragraph "All Rights Reserved" notice for the brand assets, marking the name and logo as trademarks.
3. **`/ARTMATH/LICENSE`** (or a `NOTICE` comment in its `index.html`) — "All Rights Reserved" for the artwork.
4. **README "Licensing & ownership" section** — state the three layers in plain English so the split is visible without digging through folders.

For the **artwork specifically**, you have two reasonable choices:

- **All Rights Reserved (recommended, given your concern).** Maximum protection. People can view the art on your site; they get no right to copy, reuse, or build on it. Best if you might commercialize "artistic data animation" later.
- **CC BY-NC-ND 4.0** — if you'd rather let people *share* the pieces (with credit, non-commercially, no derivatives) as marketing reach. It still blocks commercial use and modification. Choose this only if visibility matters more than control.

My default recommendation: **code MIT, brand All Rights Reserved + trademark, artwork All Rights Reserved.** It directly solves "don't license away what I've built" while keeping the generic scaffolding open.

## A few practical notes

- You already have a footer line — "© 2026 Schuck Data — a DS89 Holdco LLC company." Consider extending the brand-ownership story there or on an Artmath caption: e.g., "Artwork © 2026 Schuck Data. All rights reserved." A visible notice strengthens the reservation.
- Decide the **copyright holder** consistently. The `LICENSE` says "Dakota Schuck"; the footer says "DS89 Holdco LLC." Pick one owner (likely the LLC) and use it everywhere, or state the relationship ("© Dakota Schuck, operating as Schuck Data, a DS89 Holdco LLC company").
- `/ARTMATH/` is currently **published** (only README, LICENSE, docs/, and mockups/ are excluded from the Jekyll build). A `LICENSE` file placed in `/ARTMATH/` would be served at `/ARTMATH/LICENSE` — harmless, but if you'd rather not serve it, add `ARTMATH/LICENSE` to the `exclude:` list in `_config.yml`, or put the notice as an HTML comment in the page instead.

## If you want to be rigorous (optional)

The formal standard is **REUSE** (reuse.software) with **SPDX** identifiers: every file gets a header like `SPDX-License-Identifier: MIT` or `LicenseRef-AllRightsReserved`, and all license texts live in a `LICENSES/` directory. It's the unambiguous, machine-readable gold standard — worth it if this repo ever feeds automated compliance tooling, but heavier than this project needs today. The lightweight per-directory approach above is plenty for a marketing site.

---

## Ready-to-apply drafts (say the word and I'll put these in)

### 1. New `LICENSE` (scope preamble + unchanged MIT body)

```
Schuck Data — License & Ownership
==================================

This repository contains material under different terms:

  * SOURCE CODE (HTML, CSS, JavaScript, Jekyll config and templates) is
    licensed under the MIT License, reproduced below.

  * BRAND ASSETS — the "Schuck Data" name, the SCHUCK·DATA wordmark and
    logos (/images/schuck-data-*.png, favicon.*, og-image.png) — are NOT
    licensed under MIT. They are trademarks and © 2026 Schuck Data
    (DS89 Holdco LLC). All rights reserved. See /images/LICENSE.

  * ORIGINAL ARTWORK in /ARTMATH/ is NOT licensed under MIT. It is
    © 2026 Schuck Data (DS89 Holdco LLC). All rights reserved.
    See /ARTMATH/LICENSE.

As used in the MIT License below, "the Software" means the source code
only, and excludes the brand assets and artwork described above.

-----------------------------------------------------------------------

MIT License

Copyright (c) 2026 Dakota Schuck (DS89 Holdco LLC)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 2. `/images/LICENSE`

```
Schuck Data — Brand Assets
© 2026 Schuck Data (DS89 Holdco LLC). All rights reserved.

The "Schuck Data" name and the SCHUCK·DATA wordmark and logo files in this
directory are trademarks and copyrighted works of Schuck Data. They are NOT
covered by the MIT License in the repository root and may not be copied,
modified, or redistributed without written permission. Viewing them as part
of the published website does not grant any license to reuse them.
```

### 3. `/ARTMATH/LICENSE`

```
Schuck Data — Artmath (Original Artwork)
© 2026 Schuck Data (DS89 Holdco LLC). All rights reserved.

The generative artworks in this directory and the code that produces them
are original creative works. They are NOT covered by the MIT License in the
repository root. No license is granted to copy, modify, redistribute, or use
these works, in whole or in part, without written permission.
```

### 4. README "Licensing & ownership" section

```markdown
## Licensing & ownership

This repository mixes three kinds of material under different terms:

- **Source code** (HTML/CSS/JS, Jekyll templates, the QR generator) — MIT
  License (see `LICENSE`). Reuse freely with attribution.
- **Brand assets** — the "Schuck Data" name, wordmark, and logos
  (`/images/schuck-data-*.png`, `favicon.*`, `og-image.png`) — © 2026
  Schuck Data, **all rights reserved**, and trademarks. Not under MIT.
- **Original artwork** (`/ARTMATH/`) — © 2026 Schuck Data, **all rights
  reserved**. Not under MIT.

Viewing the public site grants no license to the brand assets or artwork.
```

Tell me which artwork option you want (All Rights Reserved vs CC BY-NC-ND) and whether the owner of record should be the LLC or you personally, and I'll apply the set.
