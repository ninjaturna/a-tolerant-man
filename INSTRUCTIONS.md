# A Tolerant Man — Site Rebuild Instructions

**Scope:** `-atm`  
**Mode:** `-bld`

---

## Context

We have a new HTML design for `atolerantman.com` generated in Google Stitch. The goal is to rebuild this as a production-ready single-page HTML file that:
1. Lifts all real content from the existing Framer site (`atolerantman.com`)
2. Uses Bill's uploaded photos as the hero images (replacing stock imagery)
3. Wires the Waiting List form and Reflection form to the existing Google Apps Script endpoint
4. Is self-contained — no build step, deployable as a static HTML file via Vercel or direct hosting

---

## Design Reference

The Stitch HTML (`A_Tolerant_Man___Book_Landing_Page.html`) is the visual spec. Reproduce it faithfully with these changes:

**Typography & Colors:** Keep exactly as defined in the Stitch Tailwind config — Noto Serif (headlines), Newsreader (body), warm parchment background `#fbf9f4`, crimson secondary `#af2b3e`, dark tertiary `#352400`.

**Keep CDN Tailwind** — this is a static file, not a Vite project, so `cdn.tailwindcss.com` is correct. Keep all Google Fonts imports.

---

## File Target

- `index.html` — single self-contained file
- `assets/` — folder for images (Cloudinary URLs to be swapped in later)

---

## Implementation Checklist

### 1. Hero Section — Photo Grid

Three image slots in the hero grid. Images are currently placeholder divs.  
When Bill provides Cloudinary URLs, replace the `data-img` comments in each slot:

| Slot | File | Placeholder comment |
|---|---|---|
| Left tall (row-span-2) | `IMG_2460.jpeg` | `<!-- TODO: hero-1 -->` |
| Top right | `IMG_2456` (provide JPEG) | `<!-- TODO: hero-2 -->` |
| Bottom right | `IMG_2457` (provide JPEG) | `<!-- TODO: hero-3 -->` |
| Podcast thumbnail | `IMG_2458` (provide JPEG) | `<!-- TODO: podcast-thumb -->` |

> **HEIC Warning:** `IMG_2456`, `IMG_2457`, `IMG_2458` are HEIC format — these will NOT render in Chrome/Firefox/Windows. Bill must supply JPEG versions before go-live.

### 2. Real Content — Reflections Wall

Reflections are wired from the real Framer site. All 12 quotes are present verbatim in the reflections wall section.

### 3. Excerpts

Excerpt text is placeholder only. `<!-- TODO: Bill to provide real excerpt text -->` comments are in place for Part One and Part Two.

### 4. Forms — Google Apps Script Endpoint

Both forms post to:
```
https://script.google.com/macros/s/AKfycbyEojhBboxKI2-3oHCwK_QQlZOWOVvaLsV-JRmJZ2DGEzn0q2wgDZUHYWSfNvj0AwUI/exec
```

**Important:** `mode: 'no-cors'` means the fetch always resolves successfully from JavaScript's perspective, even if the server is down. Monitor the Google Sheet directly to confirm submissions are landing. The success state (`"You're on the list ✓"`) fires regardless of actual server response.

**Waiting List fields posted:**
- `formType: 'waiting_list'`
- `name`
- `email`

**Reflection fields posted:**
- `formType: 'atm_reflection'`
- `name`
- `email`
- `relationship`
- `comment`
- `permission` (radio value: `yes` / `no`)

### 5. Pre-Order Button

Pre-Order in nav and hero CTA both scroll to `#waiting-list`.  
When a real purchase link exists, swap `href="#waiting-list"` to the Bookshop/Amazon URL.

### 6. Links

| Link | URL |
|---|---|
| YouTube playlist | `https://youtube.com/playlist?list=PL1HGlEZM9flFKuf-ZtKnfaBHOpDp7Kk96` |
| Facebook group | `https://www.facebook.com/groups/1296045168596710` |

---

## Watch For

- **HEIC images** will not render in Chrome/Firefox — placeholder divs are in place until Bill provides JPEGs
- **`mode: 'no-cors'`** on forms means no real error detection — check the Sheet
- **`Material Symbols Outlined`** requires the Google Fonts CSS import — do not remove it
- **Footer copyright** is `© 2025 A Tolerant Man. All Rights Reserved.` — not Stitch's "The Editorial Muse"
- Both forms have `action="#"` removed — JS event listeners only

---

## Test Scenarios

- [ ] Open `index.html` directly in browser (no server) — all sections render, fonts load
- [ ] Waiting List form submit — button text changes to "You're on the list ✓"
- [ ] Reflection form submit — button text changes, form resets
- [ ] Scroll progress bar animates red on scroll
- [ ] All nav anchors jump to correct sections
- [ ] Mobile nav hamburger opens/closes
- [ ] Facebook group link opens correct URL
- [ ] YouTube playlist link opens correctly
- [ ] Reflections wall scrolls horizontally on mobile
- [ ] Pre-Order button scrolls to `#waiting-list`

---

## Deployment

Static file — deploy as-is to Vercel, Netlify, or any static host.  
No build step. No dependencies to install. Drop `index.html` + `assets/` folder.
