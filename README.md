# Charles Binu — Portfolio Site

A single-page portfolio for Charles Binu, a graphic designer based in Kottayam, Kerala, working across branding, packaging, UI design, and advertising. Built as one self-contained HTML file — no framework, no build step, no dependencies beyond two Google Fonts loaded over CDN.

The visual identity is a **drafting/blueprint** theme: fixed ruler bars frame every edge of the viewport, hairline rules and mono-spaced labels run throughout, and copy reads like annotations on a technical drawing.

---

## Project structure

```text
index.html                         — all markup, styles, and scripts (single file)
assets/
  ├── favicon/                     — browser / PWA icons
  │     ├── favicon.ico
  │     ├── favicon-16x16.png
  │     ├── favicon-32x32.png
  │     └── apple-touch-icon.png
  │
  ├── portrait.jpg                 — about-section profile photo
  │
  └── Portfolio Works/             — all portfolio assets, organized by discipline
        ├── Branding Work/
        │     ├── letterhead/
        │     ├── logo.png
        │     ├── package/
        │     ├── price tag/
        │     ├── signboard/
        │     ├── Social Media posters & Print Media Poster/
        │     ├── UI Design/
        │     └── visiting card/
        ├── Email Newsletter Design/
        ├── Logo Works/
        ├── Package Designing/
        ├── Photo Editing/
        ├── Photo Manipulation/
        ├── Photography/
        ├── Print Advertisement/
        │     ├── bifold/
        │     ├── trifold/
        │     ├── Newspaper Tabloid.pdf
        │     └── Newspaper Tabloid_thumb.png   ← auto-generated first-page thumbnail
        ├── Social Media Advertisement/
        └── UI Designing/
```

---

## Architecture

Everything lives in three blocks inside `index.html`:

- **`<style>`** — all CSS, organized under comment headers (`TOKENS`, `RULER GUIDES`, `NAV`, `HERO`, `MARQUEE STRIP`, `ABOUT`, `SKILLS`, `PORTFOLIO`, `CONTACT / FOOTER`, `LIGHTBOX`, `LAZY IMAGE SHIMMER`, etc.)
- **`<body>`** — the markup for each section in the order they appear on the page
- **`<script>`** — vanilla JS handling animation, navigation, the portfolio category grid, the artboard modal, lightbox, and the contact form

### Design tokens

Colors, fonts, and layout constants are defined once as CSS custom properties at the top of the stylesheet (`:root`):

| Variable | Purpose |
|---|---|
| `--paper`, `--paper-dim` | Background colors (light theme) |
| `--ink`, `--ink-soft` | Primary text / dark-section background |
| `--grey`, `--grey-line` | Secondary text and hairline borders |
| `--accent`, `--accent-soft` | Brand accent (indigo) and its tint |
| `--display`, `--body`, `--mono` | The three font roles (Space Grotesk + DM Mono) |
| `--ruler-size` | Width/height of the fixed ruler bars (28 px) |
| `--maxw` | Max content width for the `.wrap` container (1180 px) |

### The ruler signature

A horizontal bar (`.ruler-h`) and vertical bar (`.ruler-v`) are fixed to the top and left viewport edges, drawn with a repeating-gradient tick pattern to resemble a drafting ruler. A `.corner-block` fills the corner where they meet. This is the site's signature visual element — `body` carries left/top padding equal to `--ruler-size` to keep content clear of the bars.

---

## Sections

| Section | `id` | Notes |
|---|---|---|
| Nav | — | Fixed; gains a bottom border once scrolled (`.nav.scrolled`); collapses to a burger menu under 760 px |
| Hero | `#top` | Name, tagline, availability pip, social links, location/status metadata. Headline letters animate in on load via staggered `translateY` transitions. Animated canvas background. |
| Marquee | — | Infinitely scrolling strip of service names — two duplicated sets of items for a seamless CSS loop |
| About | `#about` | Portrait with crop-mark overlay, bio copy, language pills, education timeline |
| Skills | `#skills` | 4-column grid of service descriptions + a row of tool chips (Photoshop, Illustrator, InDesign, Canva) |
| Portfolio | `#work` | Category card grid; clicking a card opens the artboard modal for that category |
| Experience | `#experience` | Internship timeline (Wachsen Agency 2026, ACCIMS 2024) |
| Contact | `#contact` | Contact links, inline contact form, page footer. Animated canvas background. |

---

## Portfolio system

### Category grid

Ten work categories are defined in a `categories` array: **Branding, Newsletter, Logo Works, Packaging, Photo Editing, Photo Manipulation, Photography, Print Ads, Social Ads, UI Designing**. Each renders as a card in `#catGrid` showing the category cover image, name, and piece count.

### Artboard modal

Clicking a category card opens a full-screen modal (`#workDetail`) that renders all items for that category into `#artboardGrid`. Each item is an "artboard" card showing:
- The work image (with shimmer placeholder while loading — see below)
- Title and category tag
- Sequential counter (`01 / 14`, etc.)

Titles that are bare side names (`bck`, `frnt`, `back`) are humanised by the `humanizeTitle()` function using their subfolder name — e.g. `Bifold — Front`.

### Lightbox

Clicking an artboard card opens `#lightbox` with a full-size version of the image, its tag, title, and a description line. Closes on the × button, a backdrop click, or Escape.

### PDF items

Work items pointing to a `.pdf` file (legacy path) render as a plain "Open PDF" tile — no lightbox. Items with a pre-generated thumbnail image and a separate `pdf` property (e.g. **Newspaper Tabloid**) render their thumbnail image in the grid with a small **PDF** badge; clicking them opens the PDF directly in a new tab.

The Newspaper Tabloid thumbnail (`Newspaper Tabloid_thumb.png`) was generated from the PDF's first page using PyMuPDF:

```python
import fitz
doc = fitz.open("Newspaper Tabloid.pdf")
page = doc.load_page(0)
pix = page.get_pixmap()
pix.save("Newspaper Tabloid_thumb.png")
```

### Lazy image loading

Images in the artboard modal use a custom `IntersectionObserver` for lazy loading — not native `loading="lazy"`. Each `<img>` starts life as a 1×1 transparent GIF with its real URL stored in `data-src`. When the observer fires (200 px before viewport entry), `data-src` is swapped to `src` and the shimmer animation is removed.

The shimmer CSS class (`.img-lazy`) animates a sliding gradient across the placeholder area while the real image loads, then clears on `load`.

### Portfolio data

All items live in the `portfolioItems` array inside `index.html`. Each entry is one line:

```js
{ "cat": "branding", "tag": "Branding", "title": "Visiting Card Mockup",
  "img": "Portfolio Works/Branding Work/visiting card/ChatGPT Image May 7, 2026, 12_03_52 PM.png" }

// PDF with thumbnail:
{ "cat": "print", "tag": "Print Ads", "title": "Newspaper Tabloid",
  "img": "Portfolio Works/Print Advertisement/Newspaper Tabloid_thumb.png",
  "pdf": "Portfolio Works/Print Advertisement/Newspaper Tabloid.pdf" }
```

Items are pre-sorted alphabetically by title within each category. The `cat` value must match one of the keys in the `categories` array.

---

## JavaScript behavior

All script logic is in one `<script>` block at the end of `index.html`:

| Behavior | Notes |
|---|---|
| **Hero letter reveal** | On `DOMContentLoaded`, each `<span>` in `.hero h1 .line` animates up from `translateY(110%)` with a staggered delay |
| **Nav scroll state** | `scroll` listener toggles `.scrolled` on the nav and `.show` on the back-to-top button past 40 px |
| **Mobile menu** | Burger click toggles `.open` on both the burger icon and menu panel; any menu link click closes it |
| **Reveal on scroll** | `IntersectionObserver` (threshold 12%) adds `.in` to every `.reveal` element, triggering its fade/slide transition |
| **Category grid** | `renderCategoryCards()` builds `#catGrid` from the `categories` array |
| **Artboard modal** | `openCategory(catKey)` filters `portfolioItems`, renders artboards, sets up the lazy-image observer and click handlers |
| **Lightbox** | `openLightbox()` / `closeLightbox()` manage `#lightbox`, returning focus to the trigger card on close |
| **Canvas animations** | Shared IIFE drives the particle + grid-line animations on both `#heroCanvas` and `#contactCanvas`, pausing when the section leaves the viewport |

---

## Contact form

The form submits through [FormSubmit](https://formsubmit.co), a third-party service that forwards submissions to an email inbox — no backend code required.

### Wiring

The destination email appears in two places that must stay in sync:

```html
<form action="https://formsubmit.co/charlesthottil@gmail.com" method="POST">
```

```js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/charlesthottil@gmail.com';
```

The `action` attribute is a plain-HTML fallback. The active path uses `fetch()` against the AJAX endpoint and shows a `Sending… → Message sent ✓` state on the button with an inline status line beneath it.

### One-time activation

The first submission to a new email address triggers a confirmation email from FormSubmit. Click the link inside it once before that address will receive real submissions.

### Spam protection

- A hidden `_honey` field silently discards bot submissions that fill in every visible field.
- `_captcha` is set to `false` — FormSubmit's visible reCAPTCHA challenge does not render inside an AJAX flow.

---

## Customization

| Task | Where |
|---|---|
| Add / remove / rename a work item | Edit the `portfolioItems` array in `<script>` |
| Change category covers or order | Edit the `categories` array in `<script>` |
| Update colors or type scale | Edit the CSS variables in `:root` |
| Edit section copy | Edit the text directly in the relevant `<section>` in `<body>` |
| Change the contact email | Update both the `action` attribute and `FORM_ENDPOINT` constant |
| Update social links | Edit the `<a>` tags in the hero social block and the footer |

---

## Deployment

No build step. Upload `index.html` and the entire `assets/` folder to any static host — **GitHub Pages, Netlify, Vercel**, or standard shared hosting all work.

> **Note:** The contact form's `fetch()` call requires the page to be served over `http://` or `https://`. Opening `index.html` directly as a `file://` path will cause the form request to be blocked by the browser. Use a local static server (e.g. `npx serve .`) during development.

---

## Browser support & accessibility

Targets modern evergreen browsers (Chrome, Firefox, Safari, Edge).

- All animations respect `prefers-reduced-motion` — durations collapse to `0.001ms` via a blanket media query.
- Every interactive element has a visible `:focus-visible` outline.
- The artboard modal and lightbox trap focus and return it to the triggering element on close.
- Portfolio cards and controls are fully keyboard-operable (Enter / Space to activate, Escape to close).
- Images carry descriptive `alt` text derived from the item's category and humanized title.

---

## Fonts

| Role | Family | Weights |
|---|---|---|
| Display / body | [Space Grotesk](https://fonts.google.com/specimen/Space+Grotesk) | 300, 400, 500, 600, 700 |
| Mono labels | [DM Mono](https://fonts.google.com/specimen/DM+Mono) | 400, 500 |

Both are loaded via a single Google Fonts `<link>` with `display=swap`.
