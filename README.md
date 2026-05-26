# VELOCE

A single-page car dealership website built as one self-contained HTML file. Dark cinematic aesthetic inspired by minimal SaaS landing pages — full-screen looping video hero, floating pill navigation, oversized lowercase Readex Pro typography, neutral grayscale palette, glassmorphism surfaces, and SPA-style routing with no full page reloads.

The entire application — markup, CSS, JavaScript, 150 vehicle records, procedural SVG illustrations, fonts (via Google Fonts CDN), and a Pexels-hosted background video — lives in a single file: `veloce.html`. No build step, no `node_modules`, no server required.

---

## Table of contents

1. [Quick start](#quick-start)
2. [Visual design](#visual-design)
3. [Pages and routes](#pages-and-routes)
4. [The cinema hero](#the-cinema-hero)
5. [Catalogue and data model](#catalogue-and-data-model)
6. [Visit tracking and top-5 sorting](#visit-tracking-and-top-5-sorting)
7. [Pagination](#pagination)
8. [Detail page and gallery](#detail-page-and-gallery)
9. [Forms](#forms)
10. [Navigation](#navigation)
11. [Image placeholders](#image-placeholders)
12. [Design system](#design-system)
13. [File and code structure](#file-and-code-structure)
14. [Reusable functions](#reusable-functions)
15. [Customising the catalogue](#customising-the-catalogue)
16. [Customising the cinema hero](#customising-the-cinema-hero)
17. [Browser support and responsiveness](#browser-support-and-responsiveness)
18. [Accessibility notes](#accessibility-notes)
19. [Security note](#security-note)
20. [Known limitations](#known-limitations)
21. [Credits](#credits)
22. [Licence](#licence)

---

## Quick start

1. Download `veloce.html`.
2. Open it in any modern browser — Chrome, Firefox, Safari, Edge.
3. To deploy publicly, drop the file on any static host: Netlify, Vercel, GitHub Pages, Cloudflare Pages, an S3 bucket, even a USB stick.

That is the entire setup. There are no dependencies to install, no compilation step, no environment variables, and no backend.

Optional next steps: replace the two image-placeholder slots described in [Image placeholders](#image-placeholders), or swap the Pexels video URL described in [Customising the cinema hero](#customising-the-cinema-hero).

---

## Visual design

The site commits to one tightly defined aesthetic across every page:

- **Typography** — Readex Pro at weights 300–700. Headlines use heavy negative tracking (`-0.04em`) and tight line-height (`0.95`). All copy is lowercase, top to bottom — including section titles, navigation, card titles, prices, footer.
- **Palette** — pure black `#000`, pure white `#fff`, and `neutral-900` (`#171717`) for surfaces. All text colour variation is achieved through white opacity tiers: `white/40`, `white/55`, `white/65`, `white/70`, `white/85`, `white/90`. No saturated accents.
- **Surfaces** — pill shapes (`border-radius: 9999px`) for navigation and CTAs, rounded rectangles (`20px`) for cards, soft glass effects (`bg-neutral-900/90 backdrop-blur(14px)`) for floating UI.
- **Motion** — every route transition fades and translates upward over 600ms. Cinema-hero words stagger in with a 1.5° skew over 900ms. Hover states are subtle (background tints, never transforms).
- **Layout** — generous whitespace, off-grid staggered positioning on the home hero, full-bleed listing grids elsewhere.

The result reads as one continuous brand identity. The home page is a fashion-magazine landing; the inner pages are gallery walls of the same magazine.

---

## Pages and routes

The application has nine routes wired through a single `go(routeName)` function. No full page reloads happen — `go()` toggles visibility on the relevant `<section class="route">` blocks and re-renders any dynamic content.

| Route | Description |
|---|---|
| `home` | Full-screen looping video hero with floating pill navbar, scrolls into the marquee and the top-5 most-visited rows |
| `dealership` | Grid of new dealership cars with 5-page pagination |
| `secondhand` | Grid of pre-owned cars with 5-page pagination |
| `details` | Individual car page — large gallery, full spec table, owner stats, modification history |
| `about` | Brand story, atelier banner image placeholder, three tenets |
| `contact` | Contact details and an enquiry form |
| `login` | Sign-in (gateway to the admin areas) |
| `register` | Admin registration with identity, contact, credential, and document fields |
| `admin` | Vehicle-posting form — uploads, full spec table, publishes new listings |

The navbar (a floating pill nav on every page), hamburger side panel, and footer persist across every route.

---

## The cinema hero

The home page leads with a full-viewport hero inspired by minimal SaaS landing pages such as the "securify" brief.

**Composition**

- **Background** — looping MP4 video pulled from Pexels CDN, `object-fit: cover`, plays muted with `playsInline` for iOS. A radial + linear vignette sits on top to keep text legible regardless of the current video frame.
- **Floating pill navbar** — three separate pills floating over the video:
  1. Brand pill on the left — four-shape white logomark + "veloce" wordmark.
  2. Center nav pill — `dealership · second hand · about · contact` (hidden on mobile, replaced by a circular hamburger).
  3. Right CTA pill — white-on-black "get started" button.
- **Foreground typography** — three staggered words occupy the viewport diagonally:
  - `drive` — top-left at 18%
  - `with` — top-right at 38%
  - `intent` — center-left at 58%

  Each word renders at `font-size: 14vw` (mobile) or `13vw` (desktop) with `letter-spacing: -0.04em` and `line-height: 0.95`, animating in with a 150ms-staggered skew + translate.
- **Description paragraph** — short lowercase brand-line, max-width 240px, positioned mid-left at 46% of the viewport height.
- **Three stat blocks** with diagonal `rotate(±20deg)` dividers:
  - `+142` vehicles curated — top-right
  - `+55` years in trade — bottom-left
  - `+9.8` client rating — bottom-right
- **Bottom gradient fade-to-black** for the transition into the page below.
- **Scroll hint** at the bottom-center with an animated pulsing line.

**Behaviour**

- The hero is `h-screen` (`100vh`) — the marquee and listings begin just below the fold.
- The default sticky topbar is hidden specifically on the home route (`body[data-route="home"] .topbar { display: none }`) so the floating pill navbar is the only navigation visible.
- All pill links and the "get started" CTA route through the same `go()` function — clicking `dealership` does not reload the page.

---

## Catalogue and data model

150 vehicles seed at boot — 75 dealership and 75 second-hand listings — covering major marques (Ferrari, Lamborghini, McLaren, Pagani, Koenigsegg, Aston Martin, Porsche, Bugatti, Bentley, Rolls-Royce) and historic icons (McLaren F1, Ferrari 250 GTO, Mercedes 300 SL Gullwing, BMW M1, etc.).

Each vehicle is a single object in the central `STATE.vehicles` array:

```js
{
  id: 'dealership-1',
  title: 'Ferrari SF90 Stradale',
  make: 'Ferrari',
  model: 'SF90 Stradale',
  category: 'dealership',         // 'dealership' or 'secondhand'
  pageType: 'dealership',
  price: 625000,
  status: 'Available',            // 'Available' | 'Reserved' | 'Sold'
  palette: { id, body, dark, glass, accent },
  images: [
    { label: 'Exterior — Profile', svg: () => '...' },
    { label: 'Exterior — Front',   svg: () => '...' },
    { label: 'Exterior — Rear',    svg: () => '...' },
    { label: 'Interior — Cockpit', svg: () => '...' },
    { label: 'Engine Bay',         svg: () => '...' }
  ],
  description: '...',
  ownerStats: '...',
  modifications: '...',
  table: {
    'Date of first purchase': '2025-03-14',
    'Number of years used': '0 years',
    'Changes done': 'None — factory specification',
    'Crash damaged or not': 'No',
    'Mileage': '317 km',
    'Engine model': '4.0L V8 + Hybrid',
    'Type of interior': 'Full Nappa Leather',
    'Color of interior': 'Nero Daytona',
    'Front wheel size': '20-inch Forged',
    'Back wheel size': '21-inch Forged'
  },
  visits: 412
}
```

Two listing pools live in `MODELS.dealership` and `MODELS.secondhand`. Each pool entry is a tiny seed (`{ make, model, price, engine }` plus optional `year`), and `buildVehicle()` expands it into the full schema above using rotating story templates from the `STORIES` object.

### Procedural car illustrations

Rather than ship hundreds of binary images, every car renders as inline SVG built from five reusable templates:

| Template | Description |
|---|---|
| `CarSVG.side(palette)` | Profile silhouette — used as the card thumbnail and gallery image 1 |
| `CarSVG.front(palette)` | Front three-quarter — gallery image 2 |
| `CarSVG.back(palette)` | Rear with diffuser, exhausts, light bar — gallery image 3 |
| `CarSVG.interior(palette)` | Dashboard, steering wheel, seats — gallery image 4 |
| `CarSVG.engine(palette)` | Engine bay with intake, plenum, and hoses — gallery image 5 |

Twelve colour palettes cycle through the catalogue (`PALETTES` array — red, silver, midnight, forest, gold, black, azzurro, verde, bianco, cobalt, amber, royal). This keeps the grid visually varied without any external image dependencies and keeps the file under ~155 KB.

---

## Visit tracking and top-5 sorting

Every time a user opens a car's details page, `recordVisit(id)` increments that car's `visits` counter. The home page calls `topVisited(category, 5)` to pull the five highest-visited cars per category and renders them in the marquee-adjacent rows.

```js
function recordVisit(id) {
  const v = STATE.vehicles.find(x => x.id === id);
  if (v) v.visits++;
}

function topVisited(category, n = 5) {
  return STATE.vehicles
    .filter(v => v.category === category)
    .sort((a, b) => b.visits - a.visits)
    .slice(0, n);
}
```

Initial visit counts are seeded with a deterministic sine-based distribution so the rankings feel believable on first load rather than all-zero. Counts persist for the session — a full page refresh resets them since there is no backend storage.

---

## Pagination

Listing pages use the exact controls specified:

```
« Previous   1   2   3   4   5   Next »
```

- The active page button uses a solid white background with black text (`bg-white text-black`).
- Inactive page numbers use the neutral-900/60 surface with white/85 text.
- `Previous` and `Next` visually dim and become `disabled` when the user is on the first or last page.
- 15 cards per page × 5 pages = 75 vehicles per category, matching the pool size.

State per category lives in `STATE.page.dealership` and `STATE.page.secondhand`. The `renderPagination(host, current, total, onChange)` helper is reusable for any future pageable list.

---

## Detail page and gallery

Each detail page presents:

- **Main gallery image** at 16:10 — large, rounded `18px`, soft white/8 border.
- **Four thumbnails** beneath it (5 total images per car).
- **Smooth image transitions** — clicking a thumbnail fades the outgoing image out, scales the incoming image up subtly from 1.05× to 1×, and updates the active thumbnail border. No flicker, no layout shift.
- **Vehicle title** at a `clamp(40px, 7vw, 96px)` scale, lowercase, with the same staggered fade-in animation used on the home hero.
- **Price** in Readex Pro 28px with negative tracking.
- **Description**, **first-owner stats**, and **modifications & changes** as lowercase prose blocks.
- **Specification table** — 10 rows with all the brief's required fields (date of first purchase, years used, changes done, crash status, mileage, engine model, interior type, interior colour, front wheel size, back wheel size).
- **Two CTAs** — `enquire` (links to contact) and `back to showroom/vault`.

The gallery component is reused on every detail page — same markup, same JS handlers, same animation curves.

---

## Forms

| Form | Required validation |
|---|---|
| **Contact** | First/last name, email format, message body |
| **Login** | Email format, password ≥ 6 characters |
| **Register (admin)** | Full name, age ≥ 18, region, phone (regex pattern), email, address, password ≥ 8 chars with confirmation match, NIC image upload, driver's licence image upload |
| **Admin (post vehicle)** | Title, category, price > 0, description, plus all 5 image uploads (front, back, front interior, back interior, engine), and the 10-field spec table |

All file inputs:

- Restrict to `image/jpeg, image/png` (or `image/*` for the post-vehicle form).
- Cap at 5 MB per upload.
- Show the selected filename in the field after upload.
- Restore to the default label text when the form is reset.

Sensitive form fields (passwords, document uploads) are masked, never echoed back to the DOM, and stay client-side. The login flow is a stub — it pushes a `{ email }` object into `STATE.user` to gate the admin areas.

After an admin posts a vehicle, the new listing is pushed into `STATE.vehicles`, the home page top-5 rows are re-rendered, and the user is routed to the appropriate listing page.

---

## Navigation

Three navigation surfaces work together:

1. **Floating pill navbar** — visible on every page. On home, it overlays the video hero; on other pages, the existing sticky topbar is restyled as the same three-pill arrangement so the transition between pages feels continuous.
2. **Hamburger side panel** — left-side slide-in panel triggered by the leftmost burger button. Contains every route including `login`, `admin · post`, and `register as admin`. Closes via the × button, an overlay click, or `Escape`. On mobile it covers `min(420px, 86vw)`. Smooth slide animation with `cubic-bezier(.16, 1, .3, 1)` over 450ms.
3. **Footer links** — every footer link is also a route trigger, so the footer doubles as a sitemap.

All three surfaces route through the same `go()` function. The active route is highlighted via the `is-active` class on matching `[data-route]` elements. The `<body>` element carries a `data-route` attribute for CSS targeting (e.g. hiding the sticky topbar on home).

---

## Image placeholders

There are two `<img>` slots designed for real photography. Until a `src` is set, the placeholders show a labelled hint over a diagonal-stripe pattern:

### 1. About page banner

A 21:9 full-width banner placeholder below the about-page intro. Recommended dimensions: **~1800 × 770**, dark/moody, atelier or workshop interior.

```html
<div class="img-placeholder img-placeholder--16x10" style="aspect-ratio:21/9">
  <img src="your-atelier.jpg" alt="The atelier"/>
  <div class="img-placeholder__hint">
    <b>ATELIER · BANNER IMAGE</b>
    ~1800 × 770 · interior of the workshop, garage, or storefront
  </div>
</div>
```

### 2. Legacy hero panel (not currently rendered on home)

The original right-side hero image slot remains in the codebase as `.hero__visual` for reference, in case you want to swap the video hero back to a static image layout.

When `src` is set, the placeholder hint hides automatically and the image fills its frame with `object-fit: cover`.

---

## Design system

All design tokens are declared as CSS custom properties at the top of the `<style>` block. The cinema-theme override block at the end of the stylesheet refines them. Change one variable, the whole site updates.

| Token | Value | Used for |
|---|---|---|
| `--bg-0` | `#050505` | Page background base |
| `--bg-1` | `#0a0a0c` | Slightly elevated surfaces |
| `--bg-3` | `#16161a` | Card and form surfaces |
| `--ink-0` | `#ffffff` | Primary text |
| `--ink-1` | `#e8e8ea` | Body text |
| `--ink-2` | `#a8a8ad` | Secondary text |
| `--ink-3` | `#6b6b73` | Captions, kickers |
| `--accent` | `#ffffff` | Pure white — replaces the previous royal blue |
| `--line` | `rgba(255,255,255,0.08)` | Subtle dividers |
| `--line-strong` | `rgba(255,255,255,0.14)` | Card borders |

Typography:

| Family | Usage |
|---|---|
| `Readex Pro` | Everything — display, body, monospace-style labels |

Easing:

| Curve | Usage |
|---|---|
| `cubic-bezier(.16, 1, .3, 1)` | Entrance animations, hovers, page transitions |
| `cubic-bezier(.2, .7, .2, 1)` | Snappier UI feedback (button presses) |

---

## File and code structure

```
veloce.html              ← the entire application
veloce-hero.mp4          ← home hero video (~2.9 MB, also used on login/register)
veloce-dealership.mp4    ← dealership page video (~980 KB)
veloce-secondhand.mp4    ← second-hand page video (~1.3 MB)
veloce-about.mp4         ← about page video (~1.0 MB)
veloce-contact.mp4       ← contact page video (~660 KB)
README.md                ← this file
```

All seven files must ship together when deploying — the HTML uses relative paths, so keep them in the same folder. Total video weight: ~6.8 MB. Each page only downloads its own video plus the shared home/login/register hero, thanks to `preload="metadata"` on the secondary headers.

Inside `veloce.html`, the structure is:

```
<head>
  <link>           Google Fonts — Readex Pro 300/400/500/600/700
  <style>          Design tokens, layout, components, animations,
                   then the cinema-theme global override block
                   (last so its rules win)
</head>

<body data-route="home">
  <header class="topbar">          Persistent pill navbar (hidden on home)
  <aside class="side-menu">        Persistent hamburger panel
  <main>
    <section data-page="home">          Cinema video hero, marquee, top-5 rows
    <section data-page="dealership">    Listing + pagination
    <section data-page="secondhand">    Listing + pagination
    <section data-page="details">       Reused gallery + spec table
    <section data-page="about">         Brand story + tenets
    <section data-page="contact">       Info + enquiry form
    <section data-page="login">         Sign-in
    <section data-page="register">      Admin registration
    <section data-page="admin">         Post-a-vehicle form
  </main>
  <footer>                         Persistent

  <script>
    CarSVG          Five reusable parametric SVG car illustrations
    PALETTES        Twelve colour palettes
    MODELS          Vehicle seed data (dealership + secondhand)
    STORIES         Reusable description/owner/modification copy
    buildVehicle()  Expands a seed into the full vehicle schema
    STATE           Central app state (vehicles, route, pagination, user)
    go()            SPA router — toggles routes, manages body[data-route]
    recordVisit()   Visit counter
    topVisited()    Top-N sorter for the home page rows
    carCard()       Card markup factory
    renderList()    Listing pages with pagination
    renderPagination()
    renderDetails() Detail page render with gallery wiring
    openMenu/closeMenu
    flash()         Form alert helper
    resetFileLabels(form)
    Form handlers   Contact, login, register, admin
    init()          Boot — seeds data, wires routes, opens home
</body>
```

---

## Reusable functions

All of these are exposed inside the IIFE and easy to extend:

| Function | Purpose |
|---|---|
| `go(route, opts)` | Change route without reloading; updates `body[data-route]`, scroll position, active link highlighting, and any per-page rendering |
| `recordVisit(id)` | Increment a car's visit count |
| `topVisited(category, n)` | Return the top-N visited cars in a category, sorted descending |
| `carCard(vehicle)` | Build the markup for a single car card |
| `renderTop()` | Render the home page's top-5 dealership and second-hand rows |
| `renderList(category)` | Render a paginated listing page |
| `renderPagination(host, current, total, onChange)` | Render the `« Prev 1 2 3 Next »` bar with active/disabled states |
| `renderDetails(id)` | Render the detail page including gallery + spec table |
| `buildVehicle(spec, idx, category)` | Construct a full vehicle object from a seed |
| `attachCardClicks(scope)` | Wire click handlers on every `.card` within a scope |
| `wireRoutes(scope)` | Wire all `[data-route]` elements to `go()` |
| `flash(elementId, message, kind)` | Show a transient form alert |
| `resetFileLabels(form)` | Restore file-input label text after a form reset |
| `openMenu()` / `closeMenu()` | Hamburger panel control |

---

## Customising the catalogue

Add a new car by appending to the `MODELS.dealership` or `MODELS.secondhand` arrays:

```js
MODELS.dealership.push({
  make: 'Lancia',
  model: 'Stratos HF',
  price: 740000,
  engine: '2.4L Dino V6'
});
```

Then either:

- Refresh the page — `seedVehicles()` runs on boot and picks up the new entry, OR
- Call `STATE.vehicles.push(buildVehicle(spec, idx, 'dealership'))` followed by `renderTop()` to push live without reloading.

The first 75 entries per category are seeded by default — adjust the `.slice(0, 75)` in `seedVehicles()` if you want more or fewer. Note that the pagination logic assumes 75 per category (5 pages × 15 cards), so increasing the pool means raising `totalPages` in `renderList()` accordingly.

Vehicles posted through the admin form are appended to `STATE.vehicles` at runtime and immediately appear in the correct listing.

---

## Customising the cinema hero

### Swapping the background videos

Each route has its own background video, mapped as follows:

| Route | Video file |
|---|---|
| home | `veloce-hero.mp4` |
| dealership | `veloce-dealership.mp4` |
| secondhand | `veloce-secondhand.mp4` |
| about | `veloce-about.mp4` |
| contact | `veloce-contact.mp4` |
| login | `veloce-hero.mp4` (shared with home) |
| register | `veloce-hero.mp4` (shared with home) |
| details | no background video (uses the gallery instead) |
| admin | no background video (form is content-heavy) |

To swap any video, either replace the corresponding `.mp4` file in the folder (keep the same filename), or update the `src` attribute in `veloce.html` for that route's `<video>` tag.

All videos are 1080p H.264, web-optimized with `+faststart` (metadata up front so playback starts before the full download), audio stripped. The main hero uses `preload="auto"` for an instant first-paint; the 5 secondary cinema-mini headers on other routes use `preload="metadata"` so they don't compete for bandwidth until the user navigates to them.

Recommended specs for replacement:

- 1080p (1920×1080) — sharp on every screen, fraction of the cost of 4K
- H.264 video codec, no audio track (`-an` in ffmpeg)
- 24–30 fps
- 5–15 seconds, loops seamlessly
- `+faststart` flag so streaming starts before full download
- Target file size: 0.5–3 MB

Compression command (ffmpeg) for reference:

```bash
ffmpeg -i source.mp4 -vf "scale=1920:-2" \
       -c:v libx264 -preset slow -crf 26 \
       -pix_fmt yuv420p -movflags +faststart -an \
       output.mp4
```

### Tuning the staggered words

The three hero words are absolutely positioned percentages:

```html
<h1 class="hero-title hero-title--1">drive</h1>
<h1 class="hero-title hero-title--2">with</h1>
<h1 class="hero-title hero-title--3">intent</h1>
```

Change the text, the positions (in `.hero-title--1/2/3` rules), or the size (`font-size: 14vw` mobile, `13vw` desktop) to fit your brand.

### Tuning the stat blocks

Each stat card is a small block with a number, a diagonal divider, and a sublabel. Edit the numbers and labels directly in the HTML — the layout is driven by `.stat-card--tr`, `.stat-card--bl`, `.stat-card--br` modifier classes.

---

## Browser support and responsiveness

Tested on current versions of Chrome, Firefox, Safari (desktop and iOS), and Edge.

Modern features used:

- CSS custom properties
- `backdrop-filter` (falls back gracefully to solid neutral-900 on older browsers)
- `aspect-ratio`
- Sibling-combinator selectors for placeholder hints
- `clamp()` for fluid typography
- `100svh` (small-viewport-height) for the cinema hero to avoid iOS Safari URL-bar jitter

### Responsive breakpoints

| Breakpoint | Behaviour |
|---|---|
| ≥ 1200 px | 5-column card grid, full hero with all elements |
| 960–1200 px | 3-column card grid, full hero |
| 720–960 px | Centre nav pill hides, replaced by circular hamburger; diagonal stat dividers hidden |
| 460–720 px | 2-column card grid, single-column form layout |
| < 460 px | 1-column card grid, hero scroll hint hidden, stat font scales down |

Typography is fluid via `clamp()` and `vw` units, so the hero scales smoothly from a phone to a 27" display.

---

## Accessibility notes

- All interactive elements are keyboard-reachable. Cards have `tabindex="0"` and respond to `Enter` and `Space`.
- The hamburger and close buttons use `aria-label`.
- The cinema video uses `muted` and `playsinline` so it never blocks audio focus.
- Colour contrast: white text on `rgba(0, 0, 0, 0.6)` glass surfaces passes WCAG AA at 14pt+.
- Focus rings on form inputs are clearly visible (3px white/8 ring).
- Routes use semantic `<nav>`, `<main>`, `<aside>`, `<footer>` and `<section>` landmarks.
- `Escape` closes the side menu.

Things to improve if pushing to production:

- Add `prefers-reduced-motion` overrides for the staggered title animations.
- Add `aria-current="page"` to the active nav link.
- Provide a captions track or skip-link for the background video.
- Test screen-reader flow through the SPA route transitions.

---

## Security note

This is a frontend-only demo. The "login" pushes a user object into in-memory state; nothing is sent anywhere, and a hard refresh clears it. The admin registration form does enforce — at the input layer — age ≥ 18, password ≥ 8 chars, password match, image file types, and 5 MB size caps, and avoids echoing sensitive values back to the DOM. That is the right starting point but it is not real security.

For production you need:

- A real authentication backend (session tokens, hashed passwords with bcrypt/argon2, optional 2FA)
- Server-side validation of every field
- Secure encrypted storage for uploaded identity documents
- HTTPS everywhere
- Rate limiting on the login endpoint
- CSRF tokens on form submissions
- Content Security Policy headers
- Sanitisation of admin-posted vehicle descriptions before rendering

The frontend patterns are correct; the backend is the rest of the job.

---

## Known limitations

- **No persistence** — refresh resets visit counts, user-posted vehicles, and login state. Add `localStorage` writes in `recordVisit`, `STATE.vehicles.push`, and the login handler to persist between sessions, or move to a real backend for cross-device sync.
- **Procedural car art** — the SVGs are abstract silhouettes, not photo-realistic. They are intentionally minimal so the file stays small and works offline. Swap in real photography when ready.
- **Pexels video dependency** — *no longer applies.* The hero video is now bundled locally as `veloce-hero.mp4` (2.9 MB, 1080p, H.264) and ships with the HTML. No third-party CDN dependency at runtime.
- **No backend search/filter** — the listing pages render the full pool with pagination. For larger inventories, add a filter UI and slice the array in `renderList()`.
- **Admin form skips the spec table on validation** — required fields are caught, but the spec table is treated as optional. Add stricter checks if you need every cell filled.
- **Browser back/forward buttons are not wired** — `go()` does not push to `history.pushState`. Adding it is a one-line change but currently a back button will exit the site rather than navigate routes.

---

## Credits

- **Fonts** — Readex Pro by ReadexFonts, served via Google Fonts.
- **Background video** — sourced from Pexels (free for commercial use, no attribution required), then compressed and self-hosted as `veloce-hero.mp4`.
- **Illustrations** — Hand-built procedural SVGs, no external assets.
- **Vehicle data** — Curated list of marques and models from open knowledge; prices are indicative and not real listings.
- **Design inspiration** — Minimal SaaS landing pages (the "securify" brief), cinematic automotive websites (Rolls-Royce, Pagani, Singer Vehicle Design).

---

## Licence

This project is provided as a portfolio/demo build. Use it, modify it, ship it. Attribution is not required — but if you build something nice with it, let us know.
