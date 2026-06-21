# Documentation — Charles Binu Portfolio Site

## 1. Overview

This is a single-page portfolio site for Charles Binu, a graphic designer working across branding, packaging, UI design, and social advertising. It's built as one self-contained HTML file — no framework, no build step, no dependencies beyond two Google Fonts loaded over a CDN link. The visual identity is a drafting/blueprint theme: a fixed ruler grid frames the page, hairline rules and mono-spaced labels run throughout, and copy reads like annotations on a technical drawing.

## 2. Project structure

```
index.html                              — all markup, styles, and scripts for the site
assets/                                 — all images and media referenced from index.html
  │
  ├── favicon/                          — browser / PWA icons
  │     ├── favicon.ico
  │     ├── favicon-16x16.png
  │     ├── favicon-32x32.png
  │     ├── apple-touch-icon.png
  │     ├── android-chrome-192x192.png
  │     └── android-chrome-512x512.png
  │
  ├── Branding Work/                    — brand identity project files
  │     └── gasania COUTURE.pdf
  │
  ├── Internship/                       — internship documentation
  │     └── Final Internship Report.pdf
  │
  ├── Logo Works/                       — logo design assets
  │     ├── Gasania Couture.png
  │     ├── Jhanvi logo 1.png
  │     ├── Logoooo.png
  │     └── moogliux.png
  │
  ├── Photo Editing/                    — photo retouching & editing work
  │     ├── 1.jpg
  │     ├── 2.jpg
  │     ├── 3.jpeg
  │     ├── 4.jpeg
  │     ├── 6.jpg
  │     ├── 7.png
  │     └── 8.JPG
  │
  ├── Photo Manipulation/               — composite / manipulation work
  │     ├── 2.jpeg
  │     └── BMW M3.png
  │
  ├── Photography/                      — original photography
  │     ├── Animal.JPG
  │     ├── Fauna.JPG
  │     ├── Flower.JPG
  │     ├── Landscape.jpg
  │     ├── Portrait.jpg
  │     ├── Protrait 2.JPG
  │     ├── Protrait Back light.JPG
  │     ├── Street.jpeg
  │     ├── food.JPG
  │     └── product image.jpg
  │
  ├── Print Advertisement/              — print ad layouts and collateral
  │     ├── Al - Surairat Poster 1.jpg
  │     ├── Al - Surairat Poster 2.jpg
  │     ├── Newspaper Tabloid.pdf
  │     ├── 1.jpg – 12.png  (numbered series)
  │     ├── bifold/
  │     └── trifold/
  │
  ├── Social Media Advertisement (Static Ads)/   — social media creatives
  │     ├── @wachsen 1.png – @wachsen 6.png
  │     ├── Gasania Couture 1.png – Gasania Couture 4.png
  │     ├── HighRange Social Media ad.png
  │     ├── Vikings Tattoo poster 1.png
  │     └── Vikings Tattoo 2.jpg
  │
  ├── UI Designing/                     — UI/UX mockups and screen designs
  │     ├── Curren Watch Website.jpg
  │     ├── Food delivery app.jpg
  │     ├── Login Page design.jpg
  │     ├── Registration Form.png
  │     ├── Registration Form 2.jpg
  │     ├── gasania Intsagram  UI interface.png
  │     └── website design.jpg
  │
  ├── portrait.jpg                      — about-section profile photo
  ├── branding-01.png                   — portfolio grid thumbnail
  ├── branding-02.png
  ├── branding-03.png
  ├── logo-01.png
  ├── manip-01.png
  ├── photo-01.jpg
  ├── print-01.png
  ├── print-02.jpeg
  ├── social-01.png
  ├── ui-01.jpg
  └── ui-02.png
```

The `assets` folder must sit next to `index.html`. Root-level image files (e.g. `branding-01.png`, `ui-01.jpg`) are the thumbnails used by the portfolio grid; the named subfolders hold the full-resolution source files for each discipline. The portfolio grid is generated from a JavaScript `portfolioItems` array that points to `assets/<filename>` for each card.

## 3. Architecture

Everything lives in three blocks inside `index.html`:

- `<style>` — all CSS, organized under comment headers (`RULER GUIDES`, `NAV`, `HERO`, `MARQUEE STRIP`, `ABOUT`, `SKILLS`, `PORTFOLIO`, `CONTACT / FOOTER`, etc.)
- `<body>` — the markup for each section, in the order they appear on the page
- `<script>` — vanilla JS handling animation, navigation state, the portfolio grid, the lightbox, and the contact form

### Design tokens

Colors, fonts, and a couple of layout constants are defined once as CSS custom properties at the top of the stylesheet (`:root`):

| Variable | Purpose |
|---|---|
| `--paper`, `--paper-dim` | Background colors (light theme) |
| `--ink`, `--ink-soft` | Primary text / dark-section background |
| `--grey`, `--grey-line` | Secondary text and hairline borders |
| `--accent`, `--accent-soft` | Brand accent (indigo) and its tint |
| `--display`, `--body`, `--mono` | The three font roles in use |
| `--ruler-size` | Width/height of the fixed ruler bars |
| `--maxw` | Max content width for the `.wrap` container |

Changing the palette or type scale only requires editing these variables.

### The ruler signature

A horizontal bar (`.ruler-h`) and vertical bar (`.ruler-v`) are fixed to the top and left edges of the viewport, drawn with a repeating-gradient tick pattern to resemble a drafting ruler. A `.corner-block` fills the corner where they meet. This is the site's one signature visual element and the reason `body` has left/top padding equal to `--ruler-size`.

## 4. Section-by-section reference

| Section | id | Notes |
|---|---|---|
| Nav | — | Fixed; gains a bottom border once scrolled (`.nav.scrolled`); collapses into a burger menu under 760px |
| Hero | `#top` | Name, tagline, availability pip, social links, status metadata. Headline letters animate in on load. |
| Marquee | — | An infinitely scrolling strip of service names, built from two duplicated sets of items for a seamless loop |
| About | `#about` | Portrait, bio copy, language pills, education grid |
| Skills | `#skills` | A 4-column grid of service descriptions plus a row of tool chips |
| Portfolio | `#work` | Filterable project grid; clicking a card opens the lightbox |
| Experience | `#experience` | Internship timeline |
| Contact | `#contact` | Contact links, the contact form, and the page footer |

## 5. JavaScript behavior

All script logic is in one `<script>` block at the end of `index.html`:

- **Hero letter reveal** — on `DOMContentLoaded`, each `<span>` inside `.hero h1 .line` is animated up into place with a staggered delay.
- **Nav scroll state** — a `scroll` listener toggles `.scrolled` on the nav and `.show` on the back-to-top button once the page scrolls past 40px.
- **Mobile menu** — toggling the burger button adds/removes `.open` on both the burger icon and the menu panel; tapping a menu link closes it again.
- **Reveal on scroll** — every element with the `.reveal` class is observed with an `IntersectionObserver` and gets `.in` added (triggering its fade/slide-in transition) once 12% of it is visible.
- **Portfolio grid** — built from a `portfolioItems` array (category, tag, title, description, image filename) and rendered into `#artboardGrid` via a template string.
- **Lightbox** — clicking or pressing Enter/Space on a portfolio card opens `#lightbox` with that item's image, tag, title, and description. Closes on the × button, a click on the backdrop, or Escape.
- **Filter buttons** — clicking a `.filter-btn` toggles the active state and shows/hides portfolio cards by comparing `data-filter` to each card's `data-cat`.
- **Contact form** — see section 6.

## 6. Contact form

The form submits through [FormSubmit](https://formsubmit.co), a third-party service that forwards form data to an email inbox with no backend code required.

### How it's wired

Two places in `index.html` reference the destination email, and both must match:

```html
<form ... action="https://formsubmit.co/charlesthottil@gmail.com" method="POST">
```

```js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/charlesthottil@gmail.com';
```

The `action` attribute is a fallback only used if JavaScript fails to run. The actual submission happens through `fetch()` against `FORM_ENDPOINT`, which sends the name, email, and message as JSON and shows a "Sending…" / "Message sent ✓" state on the button along with a status line beneath it.

### One-time activation

The first submission sent to a given email address triggers a confirmation email from FormSubmit. The link inside it has to be clicked once before that address starts receiving submissions — until then, the form will behave as if it succeeded, but nothing will arrive in the inbox. This re-triggers any time the destination email is changed.

### Spam protection

- A hidden honeypot field (`_honey`) silently discards bot submissions that fill in every field, including ones meant to stay empty.
- `_captcha` is set to `false`, since FormSubmit's visible reCAPTCHA challenge doesn't render inside an AJAX flow.

### Failure handling

If the request fails (offline, blocked by an extension, etc.), the form does not fail silently — it shows an inline message asking the visitor to email the address directly instead.

## 7. Customization guide

- **Portfolio content** — edit the `portfolioItems` array in the script.
- **Colors and type** — edit the CSS variables in `:root`.
- **Section copy** — edit the text directly in each section's markup.
- **Contact destination** — update both occurrences of the email noted in section 6.

## 8. Deployment

No build step. Upload `index.html` and the `assets` folder to any static host (GitHub Pages, Netlify, Vercel, or standard shared hosting). The contact form's `fetch()` request needs the page served over `http://` or `https://` rather than opened directly as a `file://` path, so a real host (or a local static server during testing) is recommended.

## 9. Browser support & accessibility

Targets modern evergreen browsers (Chrome, Firefox, Safari, Edge). All animation respects `prefers-reduced-motion`, interactive elements have visible focus states, and the lightbox and filter controls are operable by keyboard.
