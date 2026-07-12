# Certificate Card Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the artwork image display in the portfolio section of `New design/index.html` with a white "certificate" card (logo top-left, artwork centered and shrink-only, signature bottom-right), keeping the existing name-plate box unchanged.

**Architecture:** Pure CSS + vanilla JS changes inside the single existing `index.html` file (no build step, no framework). A new `.cert-card` element wraps three children (`.cert-logo`, `.cert-art-slot` containing the artwork `<img>`, `.cert-signature`) inside the existing `.artwork-img-wrap` container that `renderPortfolio()` already builds per artwork.

**Tech Stack:** Static HTML/CSS/JS, no build tooling. Manual browser verification (no test framework present in this project).

**Spec:** `docs/superpowers/specs/2026-07-12-certificate-card-design.md`

---

### Task 1: Add the cropped signature asset

**Files:**
- Create: `New design/uploads/signature-clean.png`

- [ ] **Step 1: Copy the pre-cropped signature file into the project**

The signature has already been cropped and cleaned (transparent background, scan noise removed) from `Desktop/Signature 1.png`, and saved to the scratchpad at:
`C:\Users\Pichau\AppData\Local\Temp\claude\C--Users-Pichau\6d234f51-1880-4391-8312-02b8bdabf404\scratchpad\signature-clean.png`

Copy it into the project:

```bash
cp "C:\Users\Pichau\AppData\Local\Temp\claude\C--Users-Pichau\6d234f51-1880-4391-8312-02b8bdabf404\scratchpad\signature-clean.png" "C:\Users\Pichau\Desktop\Claudio Código\Art Site\New design\uploads\signature-clean.png"
```

- [ ] **Step 2: Verify it's a 634×125 transparent PNG**

```bash
python3 -c "
from PIL import Image
im = Image.open(r'C:\Users\Pichau\Desktop\Claudio Código\Art Site\New design\uploads\signature-clean.png')
print(im.size, im.mode)
print('corner alpha:', im.convert('RGBA').getpixel((2,2)))
"
```
Expected output: `(634, 125) RGBA` and `corner alpha: (255, 255, 255, 0)` (fully transparent corner, confirming no white background box was baked in).

- [ ] **Step 3: Commit**

```bash
cd "/c/Users/Pichau/Desktop/Claudio Código/Art Site/New design"
git add uploads/signature-clean.png
git commit -m "Add cropped signature asset for certificate card"
```

---

### Task 2: Add certificate card CSS (desktop)

**Files:**
- Modify: `New design/index.html` (inline `<style>` block)

- [ ] **Step 1: Replace the `.artwork-img-wrap img` rule with the certificate card rules**

Find this existing block (around line 250):

```css
.artwork-img-wrap img {
  max-height: 100%;
  max-width: 100%;
  width: auto; height: auto; display: block; object-fit: contain;
  box-shadow: 0 12px 56px rgba(20,10,2,0.16);
  transform: translateY(14px);
  transition: transform 0.65s cubic-bezier(0.22,1,0.36,1) 0.1s;
}
.artwork-display.active .artwork-img-wrap img { transform: translateY(0); }
```

Replace it with:

```css
.cert-card {
  position: relative;
  height: 100%;
  width: auto;
  max-width: 100%;
  aspect-ratio: 806 / 1242;
  background: #fff;
  box-shadow: 0 12px 56px rgba(20,10,2,0.16);
  border: 1px solid rgba(61,47,23,0.1);
  transform: translateY(14px);
  transition: transform 0.65s cubic-bezier(0.22,1,0.36,1) 0.1s;
}
.artwork-display.active .cert-card { transform: translateY(0); }
.cert-logo {
  position: absolute; top: 6%; left: 5%; width: 20%; height: auto;
}
.cert-art-slot {
  position: absolute; top: 20%; left: 8%; width: 84%; height: 56%;
  display: flex; align-items: center; justify-content: center;
}
.cert-art-slot img {
  max-width: 100%; max-height: 100%; width: auto; height: auto; display: block;
}
.cert-signature {
  position: absolute; bottom: 6%; right: 6%; width: 46%; height: auto;
}
```

- [ ] **Step 2: Open the file in a browser to sanity-check nothing is broken yet**

Open `New design/index.html` directly in a browser (double-click it, or drag it into a browser tab — no server needed). The portfolio section will still show the old plain image display since the JS/markup hasn't changed yet (Task 3) — this step is only confirming the page still loads with no CSS syntax errors. Expected: page looks exactly as before Task 2 (new CSS classes aren't referenced by any element yet, so they have no visible effect).

- [ ] **Step 3: Commit**

```bash
cd "/c/Users/Pichau/Desktop/Claudio Código/Art Site/New design"
git add index.html
git commit -m "Add certificate card CSS (desktop)"
```

---

### Task 3: Build the certificate card markup in JS

**Files:**
- Modify: `New design/index.html` (inline `<script>` block, inside `renderPortfolio()`)

- [ ] **Step 1: Replace the image-wrap construction code**

Find this existing block (around line 811-819, inside the `items.forEach(function(a, i) { ... })` loop that builds displays):

```javascript
    var imgWrap = document.createElement('div');
    imgWrap.className = 'artwork-img-wrap';
    var img = document.createElement('img');
    img.src = a.img; img.alt = a.name;
    imgWrap.appendChild(img);
```

Replace it with:

```javascript
    var imgWrap = document.createElement('div');
    imgWrap.className = 'artwork-img-wrap';

    var certCard = document.createElement('div');
    certCard.className = 'cert-card';

    var logo = document.createElement('img');
    logo.className = 'cert-logo';
    logo.src = 'uploads/logo-cado.png';
    logo.alt = '';

    var artSlot = document.createElement('div');
    artSlot.className = 'cert-art-slot';
    var img = document.createElement('img');
    img.src = a.img; img.alt = a.name;
    artSlot.appendChild(img);

    var sig = document.createElement('img');
    sig.className = 'cert-signature';
    sig.src = 'uploads/signature-clean.png';
    sig.alt = 'Assinatura de Ricardo Souto Schützer';

    certCard.appendChild(logo);
    certCard.appendChild(artSlot);
    certCard.appendChild(sig);
    imgWrap.appendChild(certCard);
```

Everything after this (the `tag`, `counter`, `display.appendChild(...)` calls) stays exactly as it is — do not touch it.

- [ ] **Step 2: Verify in browser**

Open `New design/index.html` in a browser. Expected:
- The portfolio section (scroll down from the hero, or click "Home" then scroll) shows a white card with rounded shadow, the Cado logo in the top-left corner of the card, the artwork centered in the middle, and the cursive signature in the bottom-right corner of the card.
- The name-plate box (artwork name, dimensions, medium, WhatsApp button or "Não disponível" badge) still appears to the right of the card, unchanged.
- Click through a few artworks in the left-hand list (e.g. "Tarot", "Reflexo", "Convergência") — each should swap into the same card layout, image re-centered each time, never stretched larger than its native resolution.
- Try an `unavailable` piece (e.g. "Tarot") and an `available` piece (e.g. "Brisa") — both should show the same white card; only the name-plate's CTA area should differ (badge vs. WhatsApp button), matching current behavior.

- [ ] **Step 3: Commit**

```bash
cd "/c/Users/Pichau/Desktop/Claudio Código/Art Site/New design"
git add index.html
git commit -m "Build certificate card markup in renderPortfolio"
```

---

### Task 4: Mobile responsive behavor

**Files:**
- Modify: `New design/index.html` (inline `<style>` block, inside the `@media (max-width: 768px)` section)

- [ ] **Step 1: Replace the mobile image rule**

Find this existing block inside the `@media (max-width: 768px)` section (around line 507-515):

```css
  .artwork-img-wrap {
    width: 100%; padding: 0; margin-bottom: 14px;
    display: flex; align-items: center; justify-content: center;
    max-height: 50vh; min-height: 0; overflow: visible;
  }
  .artwork-img-wrap img {
    max-height: 50vh; max-width: 100%;
    transform: none;
  }
```

Replace it with:

```css
  .artwork-img-wrap {
    width: 100%; padding: 0; margin-bottom: 14px;
    display: flex; align-items: center; justify-content: center;
    max-height: 50vh; min-height: 0; overflow: visible;
  }
  .cert-card {
    height: auto;
    max-height: 50vh;
    width: auto;
    max-width: 100%;
    transform: none;
  }
```

(The `.cert-art-slot img` rule from Task 2 already applies `max-width: 100%; max-height: 100%` inside the card at any screen size, so no separate mobile override is needed for the artwork image itself.)

- [ ] **Step 2: Verify in browser at mobile width**

Open `New design/index.html` in a browser, open dev tools (F12), toggle device toolbar / responsive mode, set width to ~375px (a typical phone width). Expected:
- The certificate card shrinks to fit within the narrower column, keeping its portrait proportions (not stretched or squashed).
- Logo and signature remain visible and proportionally placed (top-left and bottom-right of the card).
- The name-plate stacks *below* the card (not beside it), full width, as it does today.
- Scrolling the horizontal artwork list at the top still works as before.

- [ ] **Step 3: Commit**

```bash
cd "/c/Users/Pichau/Desktop/Claudio Código/Art Site/New design"
git add index.html
git commit -m "Add mobile responsive rules for certificate card"
```

---

### Task 5: Full manual verification pass

**Files:** none (verification only)

- [ ] **Step 1: Desktop full pass**

Open `New design/index.html` in a browser at a normal desktop window width (~1400px). Click through at least 5 artworks spanning different sizes (e.g. a large one like "De Petrolina para Juazeiro" at 120×80cm, and a small one like "Sorte" at 30×16cm). Confirm for each:
- Artwork is centered inside the white card, never enlarged beyond its native pixel size (small source photos should look small/centered, not blurry/stretched).
- Logo and signature never overlap the artwork image.
- Card drop-shadow and border render correctly against the tan `.port-right` background.

- [ ] **Step 2: Filter + status pass**

Click the "Disponíveis" filter button. Confirm the card layout still renders correctly for the filtered (available-only) list. Switch back to "Todas" and spot check a `sold` or `unavailable` piece renders the same card with the correct name-plate badge.

- [ ] **Step 3: Resize pass**

Resize the browser window from desktop width down to ~375px slowly. Confirm the card transitions smoothly between the desktop and mobile CSS (no layout jump, no overflow/horizontal scrollbar appearing on the page).

- [ ] **Step 4: Report results to the user**

Summarize what was checked and any visual issues found. Do not claim the feature is "done" or "working" without having actually opened the page and looked — this is a visual/CSS feature with no automated test coverage, so eyes-on verification is the only verification.
