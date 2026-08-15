---
name: tech-ecosystem-map
description: Build or update a real-geography, layered infographic map of a region's tech/AI/capital ecosystem — an actual coastline map (not a stylized diagram), with sidebar cards linking to each company, color-coded by category, connected by leader lines to real pin locations. Use whenever the user asks to map, chart, or visualize which AI, tech, or finance companies are in a city or region; wants an "AI map of [place]" or "tech map"; asks to add, rescan, update, or refresh companies on a map like this; or references a file like "*basemap*.html". Defaults to refreshing the user's existing South Florida tech-ecosystem map (layers: AI companies, AI-adjacent infrastructure/capital, financial advisory/crypto/fintech, established pre-AI-era tech) when no other region is specified. Always verify each company's current HQ and status via a live web search before adding it — never rely on memory or an earlier layer's precedent.
---

# Tech Ecosystem Map

## What this skill produces

A single self-contained HTML file: a poster-style infographic showing a *real* geographic map (actual coastline/terrain, not a stylized or hand-drawn abstraction) of a city or region, with one or more sidebars of company cards grouped into color-coded categories ("layers"), each card linked to the company's real website and connected to its map pin by a thin leader line. Google Fonts (Playfair Display for headlines, Inter for body), cream poster background (#f4f1e7), navy accent (#1e2a5e), tan borders.

## Default target

If the user doesn't name a new region or topic — especially if they say "refresh," "rescan," "update," or "add X to the map" — look for an existing layered map file (something like `*basemap*.html`, or ask the user where it lives) and extend its current layer structure rather than starting over. The known default is a South Florida tech/AI/capital map with four layers: AI companies (purple), AI-adjacent capital/compute/energy/advisory (blue/teal/red/amber), financial advisory/crypto/fintech (navy/orange/green), and established pre-AI-era tech anchors (slate gray). Match whatever layer/color scheme is already in the file rather than inventing a new one.

For a genuinely new region or topic, build from Layer 0 (real coastline and reference geography only — no companies yet) and add one layer at a time, confirming each with the user before moving to the next. Don't jump straight to a fully populated multi-layer map unless the user explicitly asks for that in one shot.

## Sourcing real geography

Direct fetches to CDNs like jsdelivr, unpkg, or census.gov are frequently blocked in sandboxed environments (403 "blocked-by-allowlist"), even though the npm registry usually isn't. So:

1. `npm install us-atlas topojson-client` — the `us-atlas` package bundles Census cartographic boundary data (e.g. `counties-10m.json`) locally once installed, sidestepping the blocked direct fetch.
2. Use `topojson.feature()` to convert the target counties/states to GeoJSON, filtering by FIPS code or name.
3. Project lon/lat to SVG pixel coordinates with a simple equirectangular projection, but correct the aspect ratio using `cos(mean latitude)` on the longitude scale — otherwise the region looks visibly stretched or squashed.
4. Water bodies not present in low-resolution boundary packages (lakes, etc.) can be hand-approximated as a polygon at the correct real-world position and scale — disclose this approximation in the fine print rather than passing it off as precise.
5. Roads/highways: hand-trace through known lat/lon waypoints (interchanges, the cities the route passes through). This is illustrative, not survey-grade — say so if asked.

**Verifying the render without a browser:** headless browsers are often not installable in these sandboxes (missing system libs, no root to install them, and `playwright install-deps` / `apt-get` typically fail without sudo). Rely on:
- `convert` (ImageMagick) to rasterize SVG-only fragments to PNG, viewed via the Read tool. Two gotchas: ImageMagick's SVG delegate silently ignores `transform="rotate(...)"` on `<rect>` — use `<polygon points="...">` with pre-computed rotated vertices for diamond/rotated markers instead. And `stroke-dasharray="1 0"` renders as invisible — use a real dash pattern like `"8 4"`.
- PIL pixel-color sampling (`python3 -c "from PIL import Image..."`) to confirm a projected lat/lon lands on land vs. water fill color before placing a reference dot.
- For the full HTML+CSS (flexbox) layout, structural/numeric verification (below) is more reliable than trying to get pixel-perfect rendering working.

## Layout system

- `.row` is a flexbox containing, left to right: any left-side sidebar(s), a fixed-width `.map-wrap` (the real map, its own SVG with a local `viewBox`, e.g. `0 0 868 900`), then any right-side sidebar(s). Each sidebar is one category/layer.
- Map pins live inside SVG `<svg class="layer">` elements nested in `.map-wrap`, using that local coordinate space — independent of where `.map-wrap` sits in the row.
- A single full-width `svg.connectors` overlay (viewBox spanning the *entire* row width) draws leader lines from every pin to its card. Because this overlay uses row-absolute coordinates, a pin's line-x must equal its local map-x plus whatever width precedes `.map-wrap` in the row (e.g. +280 if a 260px sidebar plus a 20px gap sits to its left). **This offset is the single easiest place to introduce a bug when adding or reordering a sidebar** — recompute and rewrite every existing line's coordinates explicitly whenever the row order changes, rather than assuming old lines still line up.
- Cards are simple: `<div class="card" style="top:Npx"><p class="name"><span class="swatch-dot" style="background:COLOR"></span><a href="..." target="_blank" rel="noopener">Company</a></p><p class="cat">one-line category · neighborhood</p></div>`.
- Give each category its own color and keep a running legend. When a sidebar's card count grows, shrink card padding before letting cards overflow the fixed sidebar height — recompute even top-value spacing across the full height rather than letting later cards run off the bottom.
- A sparse, unlabeled part of the map (open water, a swamp, an empty interior) is useful found space for a low-count layer's labels instead of spinning up a whole new sidebar column — small in-map "mini-card" labels with short local leader lines back to the real pin keep the poster from growing wider every time a small layer is added.

## Research discipline (rescan before adding)

Before adding any company to the map, verify current facts with a live web search — do not rely on training data, and do not assume an earlier layer's placement pattern still applies. Specifically check the claim you're about to encode: which city/neighborhood, headquarters vs. satellite office, independent company vs. subsidiary of a larger parent. If a candidate's presence in the region can't be confirmed, leave it off and say so rather than guessing. When a rescan surfaces a correction to something already on the map (wrong city, wrong HQ status, a company that's been acquired), fix it in place and note the correction in the fine print — don't leave stale claims standing.

## Verification checklist (every edit batch, before presenting)

Run these before calling the file-present tool:
1. `grep -c 'class="card"'` equals the number of pin `<circle>` elements equals the number of `<line>` connectors added this pass.
2. Balanced tags in Python: `s.count('<div') == s.count('</div>')`, and the same for `<a>`, `<svg>`, `<g>`.
3. Pin-collision check: compute pairwise distance between every pin (including any reference dots/airport markers already on the map) and flag anything under roughly 8–10px so markers don't visually merge — nudge positions apart if they do.

Only present the file once all three pass.

## How it's built

The full reasoning behind these conventions — why real geography instead of a stylized diagram, why the offset math is the riskiest edit, why rescanning beats trusting an earlier layer — is in [`references/braintrailz.md`](./references/braintrailz.md).
