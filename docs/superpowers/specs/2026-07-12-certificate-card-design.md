# Certificate Card Design

## Overview

Replace the current artwork display in the portfolio section (`#portfolio .artwork-display`) with a "certificate" style presentation: a white card bearing the Cado Art logo and Ricardo's signature, with the artwork centered inside it. The existing name-plate box (name, dimensions, medium, WhatsApp CTA) is unchanged in content and continues to sit outside the card, to its right. This is a visual replacement of the current image display — the artwork list, filters, navigation arrows, and CTA logic all stay as they are today.

Applies uniformly to every artwork regardless of `status` (`available` / `unavailable` / `sold`), and on both desktop and mobile.

## Assets

- **Logo**: reuse the existing `uploads/logo-cado.png` (already used in nav). No new asset needed.
- **Signature**: new asset `uploads/signature-clean.png` — a cropped, transparent-background PNG isolating just the cursive signature from `Desktop/Signature 1.png`, with the surrounding scan noise (corner speckles, bottom rule line, dust) removed. Source crop: bbox (36,56)-(654,165) with 8px padding from the original 723×287 scan, producing a 634×125 image. This file needs to be copied into `New design/uploads/` as part of implementation.
- **Template.png** (`Art Site/Template.png`) is reference-only for proportions; it is not used directly as an asset since logo and signature are placed independently (see below).

## Card anatomy

- Fixed aspect ratio **806:1242** (portrait), white (`#fff`) background, `box-shadow: 0 8px 28px rgba(20,10,2,0.18)`, `border: 1px solid rgba(61,47,23,0.1)`.
- Card padding: 6% vertical / 5% horizontal.
- **Logo**: positioned `top: 6%; left: 5%; width: 20%; height: auto;`.
- **Artwork slot**: a centered flex box at `top: 20%; left: 8%; width: 84%; height: 56%`. The artwork `<img>` inside uses `max-width: 100%; max-height: 100%; width: auto; height: auto;` (same shrink-only approach as the current `.artwork-img-wrap img`) so it never enlarges past its native pixel size — only ever scales down to fit the slot.
- **Signature**: positioned `bottom: 6%; right: 6%; width: 46%; height: auto;`.
- Name-plate (`.wall-tag` / `.tag-card`) stays exactly as implemented today, positioned outside/right of the card.

## Desktop layout

`.port-right` / `.artwork-display` keeps its existing role (one per artwork, toggled `.active` on selection, same fade/translate transition timing). Inside it, the current `.artwork-img-wrap` is replaced by the new `.cert-card` element containing logo, artwork slot, and signature. The `.wall-tag` sibling element is unchanged and keeps its current position to the right.

## Mobile layout

Per the approved choice, the certificate card appears on mobile too — same structure, scaled down to fit mobile width (the existing mobile CSS already constrains `.artwork-img-wrap` to `max-height: 50vh`; the card wrapper adopts an equivalent max-height constraint while preserving its 806:1242 ratio). The name-plate continues to stack below it, full width, as it does today.

## Non-goals

- No change to the artwork list, filter buttons, prev/next navigation, WhatsApp CTA logic, or availability badges.
- No change to `sobre.html`-style bilingual content or other pages.
- No click-to-enlarge/lightbox behavior — this design *is* the full-size display (per earlier scope decision: replace, not a popup).

## Local preview

No build step. Open `New design/index.html` directly in a browser to preview (double-click or drag into a browser tab). The site has not yet been redeployed to Netlify, so this is the only way to see it until it's pushed.
