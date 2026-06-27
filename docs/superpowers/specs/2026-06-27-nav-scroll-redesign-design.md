# Nav restructure + continuous scroll — Design

## Context

Site: `lanhouse-requiem.github.io` (Cado Art portfolio), single `index.html`, vanilla JS/CSS.

Today, navigation works as a locked single-page-app: `nav` has plain text links (Home / Portfólio / Sobre / Contato), and the hero section has three CTA buttons (Ver portfólio / Sobre / Encomendar). Clicking any of them calls `showSection()`, which instantly swaps `display`/`is-open` on sections while the `<html>`/`<body>` are locked (`overflow: hidden`) — there is no real scrolling between sections. `scroll-behavior: smooth` is already declared on `html` but unused for inter-section navigation under this model.

## Goals

1. Move the three styled CTA buttons (Ver portfólio / Sobre / Encomendar) out of the hero and into the fixed nav, replacing the current plain-text nav links.
2. Replace the locked slide-swap model with real continuous scrolling: Home → Portfólio → Sobre → Contato become a normal vertical page flow. Clicking a nav button smooth-scrolls to the target section instead of swapping `display`.
3. Apply new typography and colors to the nav (decided via visual review).

## Nav restructure

- Remove `#navHome`, `#navPortfolio`, `#navSobre`, `#navContato` text links and the mobile hamburger menu that toggles them.
- `.logo` ("Cado Art") stays, links to `#home`, doubles as the "go to top" affordance.
- Add three buttons in the nav, replacing `.hero-actions` in the hero markup (which is removed):
  - `Ver portfólio` → `href="#portfolio"`
  - `Sobre` → `href="#sobre"`
  - `Encomendar` → existing WhatsApp link (`https://wa.me/5511993268188?text=...`), opens in new tab, unchanged target.
- Typography: `font-family: 'Libre Baskerville', serif; font-weight: 700;` for the logo and all three button labels. Add the Google Fonts `<link>` for Libre Baskerville 700 alongside the existing font links.
- Button styling (replaces `.btn-primary` / `.btn-ghost` for these three; class names can be reused or renamed — implementation detail):
  - **Ver portfólio**: background `#2B0F14` (near-black bordeaux), text `#E2D8C2`, `border-radius: 14px` (rounded square, not the current 99px pill).
  - **Sobre**: background `#B87C55` (terracota), text `#2B0F14`, same `border-radius: 14px`.
  - **Encomendar**: background `#B8C9B4` (sage), text `#2B0F14`, same `border-radius: 14px`.
- `btnVerPortfolio`'s existing click handler (`renderPortfolio(artworks)` to reset any filtered view before navigating) moves with it — still needed since `btnTelasVenda` can leave the portfolio filtered to `available` items.
- Drop the nav "active link on scroll" highlighting (`navItems` scroll listener and `.active` styling) — no longer meaningful once links are solid-colored buttons rather than subtle text.
- Mobile (`max-width: 768px`): hamburger menu is removed since there's no longer a text link list to collapse. The three buttons need a mobile layout — shrink padding/font-size so they fit in the nav row, wrapping to a second row only if necessary. No new interaction pattern introduced.

## Scroll behavior restructure

- Remove the "locked SPA" mechanics: `showSection()`, the `.locked` class application on `html`/`body`, and the `display:none` / `.is-open` toggling driven by JS for `#home`, `#portfolio`, `#sobre`, `#contato`.
- Sections become normal block-level content in document order (already their DOM order today: hero → portfolio → sobre → contato → footer).
- Nav buttons and the hero's `#btnTelasVenda` link become plain anchor links (`href="#portfolio"`, `href="#sobre"`) relying on native anchor scrolling + the existing `scroll-behavior: smooth` on `html`. No JS `preventDefault`/manual scroll needed for these, **except** accounting for the fixed nav's height as a scroll offset (use `scroll-margin-top: 122px` on the section elements, or equivalent, so the target section isn't hidden under the fixed nav after the jump).
- `#portfolio` keeps its current full-viewport-height layout (`height: calc(100vh - 122px)`) and internal browsing behavior (left list + right artwork display, mouse wheel / arrow keys cycle through artworks via `goTo()`) — unchanged. It simply stops being conditionally hidden (`display: none` / `.is-open` removed; it is always in flow).
- `#sobre` and `#contato` drop the forced `height: calc(100vh - 122px)` / internal `overflow-y: auto` from `.section-page` and instead size naturally to their content (existing `padding: 120px 48px` etc. already gives them reasonable height). They keep their current background/color treatment (dark for Sobre, gradient for Contato).
- `renderPortfolio(artworks)` still runs on page load (unfiltered list) — unaffected by this change.
- `btnTelasVenda`'s filter-to-available-then-navigate behavior is preserved, just becomes a normal anchor scroll instead of triggering `showSection('portfolio')`.

## Out of scope / unchanged

- Portfolio artwork data, images, WhatsApp/contact links, footer, "Tweaks" panel (mood / typography / density) and its `postMessage` integration with the parent editor.
- No changes to `Sobre`/`Contato` section content or imagery.
- No scroll-snap behavior is introduced between sections — plain continuous scroll only.

## Risk / things to verify after implementing

- Confirm the fixed-nav height offset doesn't leave a visible gap or hide section content right after an anchor jump (test all three nav buttons + the hero's `#btnTelasVenda` link).
- Confirm `#portfolio`'s internal wheel-cycle-artwork behavior still works without fighting the page's own scroll (mouse wheel over the artwork display should cycle artworks, not scroll the page — already handled by `e.preventDefault()` in the existing wheel listener, which is untouched).
- Confirm mobile nav (3 buttons in the fixed bar) doesn't overflow or wrap awkwardly on narrow screens (~375px wide).
- Re-check the "Tweaks" panel's `applyMood`/`applyTipo` functions, which directly reference `document.querySelector('nav')`/`.logo`/etc. — make sure they still find the right elements after the nav markup change.
