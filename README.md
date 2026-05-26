# VELOCE — Atelier of Velocity

A premium single-page car dealership website built as a single self-contained HTML file. Dark luxury aesthetic, royal-blue accent, cinematic serif typography, soft glassmorphism, animated cards, and SPA-style routing — designed to feel like a high-end supercar brand rather than a generic listings dashboard.

Open `veloce.html` in any modern browser. There is no build step, no dependencies, no server. Everything — HTML, CSS, JavaScript, fonts (via Google Fonts CDN), 150 vehicle records, and procedural SVG illustrations — lives in one file.

---

## Quick start

1. Download `veloce.html`.
2. Double-click to open in Chrome, Firefox, Safari, or Edge.
3. To deploy: drop the file on any static host (Netlify, Vercel, GitHub Pages, Cloudflare Pages, an S3 bucket, or even just a USB stick).

Optional: replace the two image placeholders (see [Image placeholders](#image-placeholders) below).

---

## What's inside

### Pages and routes

The application uses a custom SPA router — no full page reloads. Nine routes are wired through a single `go(routeName)` function:

| Route | Description |
|---|---|
| `home` | Hero, marquee, top-5 most-visited dealership cars, top-5 most-visited second-hand cars |
| `dealership` | All dealership (new) cars, 5 columns × 3 rows, 5 pages of pagination |
| `secondhand` | All pre-owned cars, same layout as dealership |
| `details` | Individual car page with 5-image gallery and full spec table |
| `about` | Brand story, atelier banner image placeholder, three "tenets" |
| `contact` | Contact details and an enquiry form |
| `login` | Sign-in form (gateway to admin areas) |
| `register` | Admin registration with identity, contact, credential, and document fields |
| `admin` | Vehicle-posting form — uploads images, fills the spec table, publishes to the listings |

The navbar, hamburger side panel, and footer persist across every route.

### Vehicle catalogue

150 vehicles are seeded at boot from a central data store — 75 dealership listings and 75 second-hand listings — covering most major marques (Ferrari, Lamborghini, McLaren, Pagani, Koenigsegg, Aston Martin, Porsche, Bugatti, Bentley, Rolls-Royce, plus historic icons like the McLaren F1, Ferrari 250 GTO, and Mercedes 300SL Gullwing). Each vehicle object carries the full schema specified in the brief: title, category, price, status, 5-image set, description, owner stats, modification history, a 10-field spec table, visit count, and page type.

### Procedural car illustrations

Rather than ship hundreds of binary images, every car is rendered as inline SVG from five reusable templates (`side`, `front`, `back`, `interior`, `engine`) recoloured through a palette system. Twelve colour palettes cycle through the catalogue so the grid visually varies without external assets. This keeps the file fully offline and under ~120 KB.

### Visit tracking and top-5 sorting

Every time a user opens a car's details page, `recordVisit(id)` increments that car's `visits` counter. The home page calls `topVisited(category, 5)` to pull the five highest-visited cars in each category and re-renders the rows on the next navigation back to home. Initial visit counts are seeded with a deterministic sine-based distribution so the rankings feel believable on first load.

### Pagination

Listing pages use the exact controls specified: `« Previous 1 2 3 4 5 Next »`. The active page uses the accent colour with a soft glow; inactive numbers blend into the background; `Previous`/`Next` visually dim and disable when on the first or last page. State per category lives in `STATE.page.dealership` and `STATE.page.secondhand`.

### Car details gallery

Each detail page shows one large 16:10 main image and four thumbnails below it (5 total). Clicking a thumbnail fades the outgoing image out, scales the incoming image up subtly from 1.05× to 1×, and updates the active thumbnail border — no flicker, no layout shift. The gallery component is reused on every detail page.

### Forms

| Form | Validation |
|---|---|
| Contact | HTML5 + name/email/message required |
| Login | Email format + password ≥ 6 chars (frontend-only, no real auth) |
| Register | Full identity, age ≥ 18, password ≥ 8 chars with match-check, NIC + driver's licence image uploads (JPG/PNG, max 5 MB each, type-enforced in JS) |
| Admin (post vehicle) | Five required image uploads, full spec table, description |

Sensitive form fields (passwords, document uploads) are masked and never echoed back to the UI. Uploaded images stay client-side. **This is a frontend SPA — true breach-proof security requires a backend.**

### Side menu

Left-side hamburger opens a full-height slide-in panel (≤ 86 vw on mobile) with a full-screen overlay behind it. Closes on overlay click, the × button, or `Esc`. The menu contains all routes including the admin pages.

---

## Design system

| Token | Value |
|---|---|
| Background | `#050505` with layered radial gradients and subtle noise overlay |
| Accent | `#1e40ff` (royal blue) |
| Accent glow | `#3a5cff` |
| Accent deep | `#0a1f8a` |
| Display serif | Bodoni Moda |
| Body sans | Inter |
| Mono | JetBrains Mono |
| Easing | `cubic-bezier(.2,.7,.2,1)` and `cubic-bezier(.16,1,.3,1)` for entrances |

All colours, spacing, and radii are defined as CSS custom properties at the top of the `<style>` block under `:root`. Change one variable, the entire site updates.

---

## Image placeholders

There are two `<img>` slots ready for real photography. The placeholders show a labelled hint with a diagonal-stripe pattern until you paste a `src`:

1. **Home hero**, right-hand panel — recommended 1200 × 1260, dark/moody:
   ```html
   <img class="hero__visual-img" src="your-hero.jpg" alt="Hero visual"/>
   ```
2. **About page banner** — recommended ~1800 × 770, 21:9 aspect:
   ```html
   <img src="your-atelier.jpg" alt="The atelier"/>
   ```

Once `src` is set, the placeholder hint hides automatically and the image fills its frame with `object-fit: cover`.

---

## File structure

```
veloce.html        ← the entire application (HTML + CSS + JS in one file)
README.md          ← this file
```

That's it. No `node_modules`, no `dist/`, no config.

---

## Code organisation (inside `veloce.html`)

```
<head>
  <style>                       Design tokens, layout, components, animations
</head>
<body>
  <header class="topbar">       Persistent navbar
  <aside class="side-menu">     Persistent hamburger panel
  <main>
    <section data-page="home">          Hero, marquee, top-5 rows
    <section data-page="dealership">    Listing + pagination
    <section data-page="secondhand">    Listing + pagination
    <section data-page="details">       Reused gallery + spec table
    <section data-page="about">         Brand story
    <section data-page="contact">       Contact info + enquiry form
    <section data-page="login">         Sign-in
    <section data-page="register">      Admin registration
    <section data-page="admin">         Post-a-vehicle form
  </main>
  <footer>                      Persistent
  <script>
    CarSVG          Five reusable parametric car illustrations
    PALETTES        Twelve colour palettes
    MODELS          Vehicle seed data (dealership + secondhand)
    STORIES         Reusable description/owner/modification copy
    STATE           Central app state (vehicles, route, pagination, user)
    go()            SPA router
    recordVisit()   Visit counter
    topVisited()    Top-N sorter for the home page
    carCard()       Card markup factory
    renderList()    Listing pages with pagination
    renderPagination()
    renderDetails() Detail page with gallery wiring
    Form handlers   Contact, login, register, admin
  </script>
</body>
```

---

## Reusable functions

All of these are exposed inside the IIFE and easy to extend:

- `go(route, opts)` — change route without reloading
- `recordVisit(id)` — increment a car's visit count
- `topVisited(category, n)` — fetch top-N visited cars
- `carCard(vehicle)` — markup factory for a single card
- `renderList(category)` — render a paginated listing
- `renderPagination(host, current, total, onChange)` — render the « Prev 1 2 3 Next » bar
- `renderDetails(id)` — render a car detail page with reusable gallery
- `buildVehicle(spec, idx, category)` — construct a complete vehicle object
- `flash(elementId, message, kind)` — show a form alert
- `resetFileLabels(form)` — restore file-input label text after a reset
- `openMenu() / closeMenu()` — hamburger control

---

## Customising the catalogue

The catalogue is generated from `MODELS.dealership` and `MODELS.secondhand` inside the script. Each entry is a small object:

```js
{ make: 'Ferrari', model: 'SF90 Stradale', price: 625000, engine: '4.0L V8 + Hybrid' }
```

Add a new car by appending to the array. The first 75 entries of each category are seeded on boot — change the `.slice(0,75)` in `seedVehicles()` if you want more or fewer.

Posted vehicles from the admin form are pushed into `STATE.vehicles` at runtime and immediately appear in the appropriate listing.

---

## Browser support

Tested on current versions of Chrome, Firefox, Safari, and Edge. Uses modern features (`backdrop-filter`, CSS custom properties, `aspect-ratio`, sibling-combinator selectors). Should degrade gracefully on slightly older browsers — `backdrop-filter` will simply fall back to the solid glass colour.

Responsive breakpoints:

- ≥ 1200 px — 5-column grid, full hero
- 960–1200 px — 3-column grid, navbar still visible
- 720–960 px — nav collapses into hamburger only
- < 720 px — 2-column grid, mobile-first layout
- < 460 px — 1-column grid

---

## Security note

This is a frontend-only demo. The "login" stores a user object in memory; nothing is sent anywhere, and a hard refresh clears the state. The admin registration form does enforce age, password strength, password match, image file types, and file size limits at the input layer, and avoids echoing sensitive values back to the DOM — but for production use you must add:

- A real authentication backend (session tokens, password hashing, 2FA)
- Server-side validation of every field
- Secure encrypted storage for uploaded documents
- HTTPS everywhere
- Rate limiting on the login endpoint

The frontend patterns here are the right starting point. The backend is the rest of the job.

---

## Credits

- **Fonts** — Bodoni Moda, Inter, and JetBrains Mono via Google Fonts.
- **Illustrations** — Hand-built procedural SVG; no external image assets.
- **Vehicle data** — Curated list of marques and models. Prices are indicative.

---

## Licence

This project is provided as a portfolio/demo build. Use it, modify it, ship it. If you build something nice with it, no attribution required — but it would be lovely.
