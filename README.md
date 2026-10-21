# Elsom Labs site

Single-page static site. No build step, no framework, no bundler.

```
public/
  index.html      <- the entire site: HTML + CSS + JS in one file (~440 KB)
  assets/         <- images (backdrop, matcaps, project screenshots, logos, favicons)
reference/
  routinglab-demo.html   <- standalone copy of the RoutingLab demo (reference only, NOT deployed)
HANDOFF.md        <- how the site works, section map, gotchas, constraints
.cursorrules      <- rules Cursor reads automatically
.cursor/rules/    <- same rules in the newer Cursor format
```

## Run locally

Serve it over http. Do not open index.html with a file:// URL, because the 3D textures will not load.

```
npm start            # serves ./public at http://localhost:3000
# or
cd public && python3 -m http.server 3000
```

## Deploy (Cloudflare Workers)

Git is connected to `elsomlabs/elsomlabs` on `main`. `wrangler.jsonc` publishes `public/`.

- Build command: none
- Deploy command: `npx wrangler deploy --assets ./public/`
- Root directory: `/`

Only `public/` is published. `reference/`, docs and rules are never served.

## External dependencies (loaded at runtime)

- three.js r128: `https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`
- Google Fonts: Inter, Geist Mono, Open Sans
- The Sneaker Reseller project tile embeds a live site in an iframe (only when the page is the top window)
