# Customizing the Synesthesia site

Everything lives in **one file: `index.html`**, plus three assets next to it
(`wordmark.png`, `ambience.mp3`, `og.png`). No build step, no frameworks — edit
the file, refresh the browser, done.

Tip: find things with **Cmd+F** using the search strings below (line numbers
shift as you edit, so this guide avoids them).

## Your list — where each item lands

| Change | Difficulty | Where |
|---|---|---|
| Logo (your new SVG/PNG) | Easy | "Logo" below — easiest: export a PNG and replace `wordmark.png`, zero code edits |
| Date, descriptions, copy | Easy | "Text & content" below |
| Line-up names | Easy | Marquee row in the table below |
| New font | Easy for the body font; advanced for the display font | "Body font" + the Fraunces warning |
| Background from your visual | Moderate | "Swapping in a background image" below |
| Merch reservation + email signup | Moderate | New section, not an edit — "Adding an email signup" below |
| Effects / movement | Advanced | Lives in the animation script — see "Handle with care" |

## Previewing your changes

⚠️ Don't just double-click `index.html` — Chrome blocks the logo mask on local
files and the SYNESTHESIA wordmark will disappear. Serve it instead. From this
folder, in a terminal:

```
python3 -m http.server 8000
```

then open http://localhost:8000. (Or use the "Live Server" extension in
VS Code.)

---

## Safe to change — text & content

All of this is plain text in the HTML. Change the words, keep the tags.

| What | Find (Cmd+F) |
|---|---|
| Date line above the logo | `Autumn 2026` (appears **3×**: eyebrow, footer "When", and the `<title>`/meta tags — change all of them) |
| Hero paragraph | `one sense fires, another responds` |
| Statement section | `Music, paired with` |
| The four sense cards | `Live sets are the source signal` |
| Lineup section text | `First names announced with the date` |
| Scrolling artist names (marquee) | `Noche Abierta` — these are **placeholders**; each name appears twice (the strip repeats to loop seamlessly), so update both copies |
| Ticket tiers: names, blurbs, prices | `First Listener` |
| "Best value" chip | `Best value` |
| Footer: when / where / tickets / socials | `Langstrasse 112` |
| Browser tab title + link-preview text | `<title>` and the `og:` / `twitter:` meta tags at the top |

### Hooking up real ticket links

The two "Get tickets" buttons currently scroll to the tiers section
(`href="#tickets"`), and the footer "Eventfrog ↗" points at `#tickets` too.
When you have the Eventfrog page:

1. Footer: change `<a href="#tickets">Eventfrog ↗</a>` to your real URL and add
   `target="_blank" rel="noopener"`.
2. Bottom CTA (`Get your ticket now`): point its `href` at the Eventfrog URL.
3. Optionally wrap each tier card in a link to the specific ticket type.

Same when Instagram exists: search `Instagram ↗` and replace the `#top` href.

## Safe to change — look & feel

**Background and text colors** — search `:root {`. `--bg` is the page
background, `--ink` the text color, `--on-accent` the text color used on
accent-colored buttons.

**Accent color** — the accent *migrates as you scroll* (violet → cyan → amber).
It's driven by three HSL color stops in the script — search:

```
const stops = [[268,78,68],[190,75,62],[24,85,64]];
```

Each `[hue, saturation%, lightness%]` triple is one phase of the scroll. Same
hue in all three = one fixed accent color. (The `--accent` in `:root` is only
the pre-JavaScript fallback — change it to match your first stop.)

**Hero rainbow gradient** — the colored sweep across the big SYNESTHESIA
wordmark is its own list of colors: search `linear-gradient(100deg` inside the
`h1.wordmark` style block. First and last color should match so the sweep loops
cleanly.

**Logo** — `wordmark.png` is used as a *mask* (the shape is the logo; the page
pours color through it — that's how it gets the rainbow sweep and the white
ribbon version from one file). Replace it with any PNG where the logo is solid
and the background fully transparent. Keep roughly the same wide aspect ratio
(current file is 2172×724). Used in two places automatically: hero + the top
ribbon. An SVG works too (search `wordmark.png` — it appears in two `mask:`
rules — and change the filename), but a transparent PNG with the same name is
the zero-edit route.

**Body font** — the site loads Fraunces (display), Bricolage Grotesque (body),
IBM Plex Mono (technical labels) from Google Fonts. Swapping the **body** font
is safe: change the Google Fonts `<link>` and the `--body` / `font-family`
references.

⚠️ **The display font (Fraunces) is load-bearing.** The breathing/weight
animation drives Fraunces' variable-font axes (`wght`, `SOFT`, `opsz`). A
replacement display font must be a variable font with a wide weight axis, or
the headings will stop breathing and letter spacing will misbehave. Recommend
keeping Fraunces.

**Ambient sound** — replace `ambience.mp3` (keep the filename) with any track
you have the rights to. Current file is CC0 (Freesound #568142, "Cinematic
Handpan"), so it's safe to keep. It loops; a track with a clean start/end
loops best. Sound stays off until a visitor turns it on — leave it that way
(browsers block autoplay audio anyway).

**Link-preview image** — `og.png` (1200×630) is what WhatsApp/Instagram show
when the link is shared. If you change colors/logo, re-screenshot the hero at
that size and replace the file.

## Swapping in a background image

The current background is two animated radial gradients that orbit each other
(search `--g1x` to see the moving pieces) plus a grain overlay. To use your own
visual instead:

1. Add your image to this folder (e.g. `background.jpg`, web-compressed —
   aim for under ~500 KB).
2. Search `body {` in the CSS and add
   `background:url(background.jpg) center/cover fixed no-repeat;` —
   put a dark color first (e.g. `#020e1f url(...)`) so text stays readable
   while it loads.
3. To stop the orbiting gradients underneath, search `--g1x` in the script and
   delete (or comment out) the four `--g1x/--g1y/--g2x/--g2y` lines.

Keep the contrast high: the text is near-white, so the visual should stay
dark, or add a dark overlay on top of it.

## Adding an email signup (merch reservation)

The site is static, so a form needs an outside service to receive submissions.
The simplest free route is [Formspree](https://formspree.io):

1. Create a free form there; it gives you an endpoint like
   `https://formspree.io/f/abcd1234`.
2. Copy one of the existing `<section>` blocks in `index.html` as a template
   (the tickets section is a good skeleton), give it a heading like
   "Reserve your merch", and inside it add:

   ```html
   <form action="https://formspree.io/f/YOUR_ID" method="POST">
     <input type="email" name="email" required placeholder="you@email.com">
     <button class="cta" type="submit">Reserve</button>
   </form>
   ```

3. Submissions arrive in your Formspree dashboard/inbox. Style the input to
   taste — the `.cta` class makes the button match the rest of the site.

New section headings automatically pick up the scroll animation — headings are
found by their `display` class, so use the same pattern as existing sections
(`<h2 class="display twist enter">`).

## ⚠️ Handle with care

Everything inside `<script>` **below** the `const stops` line: the
letter-splitting, the font-loading logic, and the scroll-driven flip animation
are calibrated together (per-letter 3D perspective, frozen letter widths,
bottom-of-page handling). Editing there can make headings overlap or mush at
the bottom of the page. If you want to tune the motion, the safest dials are
the numbers themselves (e.g. the `3.2` in the hero sway, the `45` in the
weight breathing) — change one value at a time and test on both desktop and
phone before touching anything structural. Commit before experimenting so you
can always roll back.

Also: keep the `.sr` span (`<span class="sr">Synesthesia</span>`) — it's what
screen readers and search engines read, since the visible logo is an image.

## Exploring further — bigger visual effects

The site's effects are all CSS + vanilla JavaScript (variable fonts,
gradients, transforms) — no libraries. If you want to go further, in rough
order of effort:

1. **CSS only** — a surprising amount is possible (animated gradients,
   blend modes, filters). Learn the building blocks at
   [MDN: CSS animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animations)
   and browse [Codrops](https://tympanus.net/codrops/) for effect ideas with
   source code.
2. **Canvas 2D** — particles, waves, generative textures in plain JavaScript:
   [MDN: Canvas tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial).
3. **Three.js (WebGL)** — the big leagues: 3D blobs, shader gradients, the
   [blobmixer](https://blobmixer.14islands.com/) aesthetic. Start with the
   [three.js docs](https://threejs.org/docs/) and the
   [official examples](https://threejs.org/examples/); the paid course
   [Three.js Journey](https://threejs-journey.com/) is widely considered the
   best structured path. No build tools needed — three.js loads from a CDN
   with an import map (see the docs' "Installation" page).

**Clean way to add any of these here:** create a full-screen canvas layered
*behind* the page —

```html
<canvas id="fx" style="position:fixed; inset:0; z-index:-1"></canvas>
```

— and drive it from its own `<script>` at the end of the file. That way your
effect replaces the gradient background without touching the heading/scroll
animation system. Fair warning: WebGL is a genuine learning curve — budget
evenings, not minutes — but it's a rewarding one.

## Deploying

The live site is on Vercel (`synesthesia-zurich.vercel.app`). Once the repo is
transferred and connected to your own Vercel account, every push to `main`
deploys automatically — no other steps. Custom domain: add it in Vercel →
Project → Settings → Domains, then point the domain's DNS where Vercel tells
you.
