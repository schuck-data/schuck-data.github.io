# Grayware / Web-Filter Flagging — Analysis & Recommendations

*Date: 2026-05-26 · **Recommendations only — no code was changed.** Implement selectively after review.*

> If you can tell me **which** system flagged the site and **what category/message** it gave (e.g., "Uncategorized," "Suspicious," "Newly Registered Domain," a SmartScreen warning, a Zscaler block page), I can target this much more precisely. The analysis below covers the most probable causes in priority order.

## What "grayware" classification actually means here

Secure web gateways and endpoint tools used on government networks (Zscaler, Broadcom/Symantec, Forcepoint, Palo Alto, Cisco, Fortinet, Webroot/BrightCloud) and browser reputation systems (Google Safe Browsing, Microsoft SmartScreen) don't "scan your JavaScript for malware" the way an antivirus scans a file. They assign your **domain** a *category* and a *reputation score* from a mix of crawler heuristics, domain age/history, and traffic patterns. A strict government configuration then blocks whole buckets — "Uncategorized," "Newly Registered Domain," "Suspicious," "Potentially Unwanted." So a site can be totally benign and still be blocked because of **what bucket it landed in**, not because of bad content.

That reframing matters: the single biggest lever is almost never a code change — it's **getting the domain correctly categorized**.

## Most likely triggers, in priority order

**1. New / uncategorized domain (most probable root cause).**
`schuckdata.com` is recently registered with little traffic history. Many government filters block "Uncategorized" or "Newly Registered Domain" by default policy. This alone explains a "potentially problematic" flag with *zero* bad content on the page. Everything else below is secondary to this.

**2. Redirect chains and social-media destinations.**
You have two redirect patterns that look, to a heuristic classifier, like the cloaking/phishing playbook:
- The five parked domains 301-redirecting to `schuckdata.com`.
- More importantly, the `QRLIBRARY/SVSD1` and `SVSD2` **meta-refresh redirects to Facebook**. Instant meta-refresh hops (`<meta http-equiv="refresh" content="0;url=…">`) are a well-known "suspicious redirect" signal, and Facebook is itself blocked or restricted on many government networks — so a page that bounces straight there can get the *source* page flagged.

**3. Continuous `requestAnimationFrame` canvas animation.**
The homepage hero and the six Artmath canvases run perpetual animation loops doing heavy per-frame math (particle networks, a projected 4-D tesseract). Sustained high CPU plus canvas drawing is exactly the behavioral fingerprint some endpoint tools associate with **cryptojacking** or **canvas fingerprinting** (the hero also reads `devicePixelRatio`, a fingerprinting signal). This is more likely to raise a behavioral/"this page pegs the CPU" flag than a hard category block — but it's real, and the Artmath canvases never stop, even off-screen or under `prefers-reduced-motion`.

**4. External CDN script with no Subresource Integrity.**
The QR page loads `qrcode-generator` from cdnjs with no `integrity` hash and no Content-Security-Policy. Loading third-party executable JavaScript with nothing pinning its contents is a generic "unverified external script" heuristic and a genuine supply-chain weak point. cdnjs is reputable; SRI is the standard mitigation.

**5. "Hidden" / noindex backstage pages.**
`/BACKSTAGE/`, `/QR/`, `/ARTMATH/` are `noindex,nofollow`, reachable only by direct URL, behind a near-invisible em-dash link. On its own this is low-risk, and your `robots.txt` reasoning (don't enumerate hidden paths in a public Disallow) is actually correct — I would **not** change it. But combined with #2, it adds to a "this site does things it doesn't advertise" picture that classifiers weight.

**6. Dense, minified-looking inline JavaScript.**
The hero/QR/Artmath scripts are long single-line, terse code. Static scanners sometimes score "obfuscated-looking" inline script as mildly suspicious. Low likelihood, cheap to improve.

**7. No Content-Security-Policy.** Not a trigger by itself, but a meta CSP is a positive signal of a well-run site and constrains exactly the behaviors (inline + external script) scanners worry about.

## Recommended mitigations (prioritized)

### Tier 1 — highest impact, do these first

- **Submit the domain for categorization with each major vendor.** This is *the* fix for a new domain being blocked. Check the current category, and if it's "Uncategorized" or wrong, request "Business/Economy" or "Information Technology." Table below.
- **Confirm HTTPS is enforced** in GitHub Pages settings (Settings → Pages → "Enforce HTTPS") and the cert is valid. An http-only or cert-warning site is an automatic trust hit.
- **Make the site easy for a *human* reviewer to categorize as a legitimate business.** You already help yourself here — real business name, founder bio, phone number, physical contact path. Keep WHOIS/registration looking like a real business (a fully privacy-proxied brand-new domain reads as throwaway). Consider adding a one-line, visible business description and a `/.well-known/security.txt`.

### Tier 2 — content hardening

- **Add Subresource Integrity to the QR page's CDN script:**
  ```html
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcode-generator/1.4.4/qrcode.min.js"
          integrity="sha512-…"  crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  ```
  (Get the hash from cdnjs's "Copy SRI" button, or generate it: `curl -s <url> | openssl dgst -sha512 -binary | openssl base64 -A`.)
- **Add a meta Content-Security-Policy** to `_layouts/default.html` `<head>`. GitHub Pages can't send HTTP headers, so a `<meta http-equiv="Content-Security-Policy">` is the only option — note it ignores `frame-ancestors` and can't report violations, but it still constrains scripts. A starting policy that fits this site: `default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src https://fonts.gstatic.com; img-src 'self' data:`. (The inline scripts mean you still need `'unsafe-inline'` unless you move them to files or add hashes — moving them out is the cleaner long-term path.)
- **Rework the Facebook meta-refresh redirects.** Replace the instant `0;url=` meta-refresh with a tiny visible interstitial — "Taking you to our Facebook page… click here if it doesn't load" — with a real clickable link and a 1–2s delay. QR codes keep working, but you drop the auto-redirect-to-social signal that classifiers dislike.
- **Gate the canvas animations harder.** The hero already pauses off-screen and under reduced-motion — good. The **Artmath** canvases do neither: they should honor `prefers-reduced-motion` (render one static frame) and pause when scrolled out of view or the tab is hidden. This helps accessibility, battery/CPU, *and* the cryptojacking-style heuristic.

### Tier 3 — optional polish

- De-minify the inline scripts (or move them to real `.js` files) so they don't read as obfuscated.
- Leave backstage `noindex` as-is — but remember `noindex` is privacy-by-obscurity, not security. Anything genuinely sensitive shouldn't live behind it.

## Vendor categorization / review tools

Check your current category on each, and submit a correction where it's missing or wrong. (URLs current as of 2026-05-26 — confirm before submitting.)

| Vendor (who uses it) | Lookup / submit tool |
|---|---|
| **Broadcom / Symantec WebPulse** (Edge SWG, many gov/enterprise) | https://sitereview.symantec.com/ — "Check Category," then dispute |
| **Palo Alto Networks** (PAN-DB) | https://urlfiltering.paloaltonetworks.com/ — search, then "Request Change" |
| **Cisco Talos** (Umbrella/Firepower) | https://talosintelligence.com/reputation_center |
| **Forcepoint** (ThreatSeeker / CSI) | https://csi.forcepoint.com/ |
| **Fortinet FortiGuard** | https://www.fortiguard.com/webfilter — lookup + "Request Review" |
| **Webroot BrightCloud** | https://www.brightcloud.com/tools/url-ip-lookup.php |
| **Zscaler** | https://sitereview.zscaler.com/ (or a support ticket) |
| **Google Safe Browsing** (Chrome, many tools) | https://transparencyreport.google.com/safe-browsing/search + check Search Console → Security Issues |
| **Microsoft SmartScreen** (Edge/Defender) | Report/dispute via the SmartScreen feedback page |

## How to confirm what's really happening

Before changing code, look up `schuckdata.com` on two or three of the tools above. If it comes back "Uncategorized" or in an unexpected bucket, you've found the cause and the fix is the submission, not the site. Also check Google Search Console's Security Issues panel and the Safe Browsing status page to rule out a browser-level "deceptive site" flag (a different mechanism from a gateway block).

**Bottom line:** the most probable cause is a new/uncategorized domain plus the auto-redirects to Facebook — both fixable without touching the design. The canvas animations and the no-SRI CDN script are real but secondary. I'd start with vendor categorization and the redirect interstitial, then SRI + the Artmath animation gating.
