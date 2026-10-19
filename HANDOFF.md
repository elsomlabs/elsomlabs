# Handoff: how this site works

This file records everything that was decided while the site was built. Read it before changing anything.

## Ground rules from the owner (Matt)

- Make exact, narrow changes. Do not "improve" things that were not asked about.
- Copy screenshots/references closely. Do not add extra features.
- Do not invent metrics. Illustrative mock data is fine if labelled as such.
- When asked to revert, revert immediately and fully.
- Keep one file. Do not split index.html into modules or add a framework unless explicitly asked.

## File layout

`public/index.html` has everything. Rough line map (lines drift as you edit; search for the ids/names instead):

| What | Where to find it |
|---|---|
| CSS | the first `<style>` (top of file, ends ~line 651) |
| Wide-screen scaling script | tiny inline `<script>` right after the CSS (sets `window.__Z` and `--z`) |
| Header/nav | `<header class="top">` |
| Sections, in order | `#hero`(0) `#services`(1) `#work`(2) `#process`(3) `#webdesign`(4) `#software`(5) `#pricing`(6) `#contact`(7). `#faq` sits between Pricing and Contact; it is in the scroll-lock but not in the 3D scene list |
| RoutingLab demo | inside `#software`: `<iframe id="rlFrame">` plus `<template id="rlSrc">` holding the whole demo HTML; a small script sets `iframe.srcdoc` |
| Main JS | `<script src=...three.min.js>` then a big inline script with several IIFEs |
| Portfolio carousel | IIFE that starts with `var ring=document.getElementById('orbRing')`; project list is `var WORK=[...]` |

## Features that are easy to break

**Section-lock scrolling.** Wheel/touch snaps between the 8 sections. `on()` is `fine.matches && innerWidth>860`. `LOCKN=1e9`. The wheel handler skips events where `e.defaultPrevented`. The portfolio rail and the embedded demo forward or block wheel events on purpose.

**Wide-screen scaling.** JS sets `--z` and `window.__Z` from `k=min(w/1440,h/880)`, clamped to 2.4, only when width > 1500. CSS applies `zoom:var(--z,1)` to `section > .wrap, .top .wrap, .hud, .scrollcue`. Canvas DPR is multiplied by `__Z`. The contact sphere (`.cball`) is deliberately NOT scaled; scaling it overlaps the form.

**Portfolio carousel (`#work`).** Cards sit in `#orbRing` inside `#orbStage`. In `lay()`: `cw=vw>860?Math.min(vw*.36,h*1.5,720):Math.min(w*.72,h*1.5)`, `ch=cw*10/16`, `sp1=cw*1.0`, `sp2=cw*.62`, `dz=cw*.5`. In `place(f)`: rotateY is `sg*min(a,1)*32 + sg*max(0,min(a-1,1))*10`. Non-centre cards get `.orb-card.dim{filter:brightness(.32) saturate(.6)}`. Wheel only rotates it when the pointer is over a card; drag, click-to-centre and click-centre-to-pop-out all work. The pop-out uses a FLIP animation, so keep `.orb-pop[hidden]{display:none!important}`. Starting tile is `Math.floor((n-1)/2)`.

`WORK` order: Stunell Wills, Ledgerline, Atlas Ops, Sneaker Reseller, Saltmarsh, Studio Nord. The Sneaker entry has an `embed:` URL. The live iframe (`iframe.orb-live`, 1400px wide, scaled by `k=cw/1400`) is only injected when `window.top===window.self`. Inside any framed preview (for example Claude's artifact viewer) the screenshot `assets/work/sneaker.jpg` shows instead. Its title is still "Sneaker Reseller" because the live site still says "[STORE NAME]"; swap in the real name when known.

**RoutingLab demo.** Five screens: RouteMap, Schedule, LiveTech, Banks, Techs. It is scaled to fit with `fit()`/`SC`. The map is generated in JS (`baseMap()`, `regionMap()`). LiveTech has 20 fictional techs with white two-line labels and leader lines; the viewBox is `200 150 1200 750` (`LVB`). The city is named "Chicago". Scrollbars are hidden. A script at the end forwards wheel events to the parent page.
`reference/routinglab-demo.html` is the same demo as a standalone file. If you change one, change the other.

**Process tiles.** Radar, scan, blocks and globe canvases, plus an integrations marquee at the bottom (logos in `assets/logos/`).

**Matcap cube.** 27-instance three.js cube with matcap materials (`assets/matcap-*.jpg`).

## HARD CONSTRAINTS for the RoutingLab demo (privacy / proprietary)

The real RoutingLab app is proprietary and must never be linked or exposed. The demo must stay:

- Invented data only. No real customer names, addresses, job or work-order numbers, technician names, vehicle positions, or employer data (Better Protection, Assurant Fire, ServiceTrade).
- No API calls, no backend, no real code from the real app, no links to the real app.
- Labelled "Product demo · sample data".
- Free of the owner's email address and any real personal identifiers.

## Testing tips

- Serve over http (`npm start`). 3D textures fail on file://.
- Headless Chromium needs SwiftShader for WebGL (`--use-gl=angle --use-angle=swiftshader --enable-unsafe-swiftshader`).
- Test at about 1440, 1895 and 2560 wide, and at about 400 wide for mobile.
- To test the Sneaker live embed you must be on a real top-level page, not in a framed preview.

## Open ideas (none of these were requested)

Working contact form; real prices instead of `$xxx`; remove "Prices exclude VAT"; privacy policy; SEO / share image; about section; testimonials; case studies; FAQ; lazy-load heavy assets; booking link; analytics.
