# Synesthesia — event site

One-page site for **Synesthesia** (Klaus, Langstrasse 112, Zürich · Autumn
2026). Live at **https://synesthesia-zurich.vercel.app**.

Built by [Seiri Studio](https://seiristudios.com) as a single self-contained
page: no frameworks, no build step. `index.html` plus three assets is the whole
site.

```
index.html       the entire site — markup, styles, and animation script
wordmark.png     the SYNESTHESIA logo (used as a mask; recolored by the page)
ambience.mp3     ambient sound bed (CC0, off by default, toggle top-right)
og.png           link-preview image (1200×630) for WhatsApp/Instagram shares
CUSTOMIZING.md   ← how to change things: copy, dates, colors, logo, fonts…
```

## Editing

**Read [CUSTOMIZING.md](CUSTOMIZING.md)** — it maps every planned change
(logo, date, line-up, fonts, new sections) to exactly where it lives, and
marks the few places not to touch.

## Preview locally

Don't open `index.html` by double-clicking — Chrome blocks the logo mask on
local files. Serve it instead:

```
python3 -m http.server 8000
```

then open http://localhost:8000.

## Deploying

Import this repo at [vercel.com](https://vercel.com) (framework preset:
"Other", no build command, output directory: root). Every push to `main` then
deploys automatically. Custom domain: Project → Settings → Domains.
