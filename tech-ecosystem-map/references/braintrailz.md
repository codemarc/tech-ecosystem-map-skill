# Braintrailz Composition Notes: The Tech Ecosystem Map Skill

Trail markers for why this skill is built the way it is. Not a changelog. The reusable parts are the point.

## The origin: "but that is not a map!"

The first version of this abandoned the geographic visual entirely. It was a single-screen-fit problem: a real coastline, once populated with enough companies, wanted to scroll. The fix at the time was to redraw it as an abstract subway-line diagram — zone panels, connector lines, no land or water anywhere.

The verdict came back in five words: "but this is not a map!"

That correction is the whole design brief. The single-screen constraint was real and had to be solved, but not by abandoning the thing that made it a map in the first place. The fix was a landscape orientation and a compressed-but-still-recognizable coastline, not a retreat into abstraction. Constraints get solved inside the form, not by discarding it.

## Real geography, not a hand-drawn approximation

Early attempts sketched a coastline by eye — plausible-looking, wrong in the details. The difference between "looks like Florida" and "is Florida" turned out to matter to the person reading it.

The fix was sourcing actual Census cartographic boundary data. Direct fetches to jsdelivr, unpkg, and census.gov were blocked in the sandbox (403, blocked-by-allowlist) — but `npm install us-atlas` worked, because the npm registry wasn't behind the same block and the package bundles the boundary data locally once installed. When a direct fetch is blocked, check whether the same data is available as a package dependency before giving up on "real" and falling back to "plausible."

## Layer by layer, confirmed each time

The build went from a real coastline, to roads and airports, to reference cities, to the first company layer — one layer at a time, with a check-in after each. This was slower than generating a fully populated map in one pass, and it caught things a single pass would have missed: after the first company layer landed, a review pass ("are there any we omitted that are definitely part of the moat") turned up two real gaps before the layer was called done.

Building in layers is not just a UI decision. It's the mechanism that makes the review catch things.

## Rescanning beats trusting the last layer

Every new company added to a later layer got a live search before it went on the map — not because the pattern from an earlier layer might be wrong, but because company facts move. Two corrections came directly out of this: a facility assumed to be at a company's headquarters city was actually on a university campus forty miles off, and a company on the shortlist for a financial layer had no confirmable regional presence at all and got dropped rather than guessed into place.

The discipline is specific: verify the claim you're about to encode (which neighborhood, HQ vs. satellite, independent vs. subsidiary), not just that the company exists. "I've heard of them" is not the same bar as "I checked."

## The offset math is the riskiest edit

Every sidebar added to the map lives in row-absolute coordinates inside a single overlay SVG, while every pin lives in the map's own local coordinate space. The translation between the two is one number: how much width sits between the row's left edge and the map. That number changes every time a sidebar is added, removed, or reordered — and every *existing* line in the connector overlay has to be rewritten to match, not just the new ones.

This bit twice: once when a financial-companies layer was asked to run on the opposite side of the map from the others, and once when a fourth sidebar was added and then later removed in favor of in-map labels. Both times, the fix was the same: stop patching individual lines and recompute the whole coordinate set from the current row order, treating every existing line as suspect until reverified.

## Found space beats a fourth column

A four-company "established tech" layer briefly got its own sidebar — another 280px added to the poster width for four cards. It was reworked as small in-map labels dropped into an empty, unlabeled interior of the map (in this case, the Everglades) with short leader lines back to the real pin locations.

The general version: a low-count layer doesn't need a whole new column. If the map has genuinely empty space, use it. The poster stays the same width, and the labels sit closer to their pins than a distant sidebar ever would.

## Verify structurally, not visually

No headless browser was reliably installable in the sandbox this was built in — `playwright install-deps` and `apt-get` both need root that wasn't available. Rather than chase pixel-perfect rendering, verification shifted to counting: cards match pins match connector lines, every opening tag has a matching close, and no two pins land within collision distance of each other.

This turned out to be *more* reliable than visual inspection would have been for this kind of layout bug — an offset math error doesn't always look wrong at a glance, but it always shows up as a line count that doesn't match a card count.

## The composition pattern, generalized

1. **Solve constraints inside the form.** A single-screen limit is a layout problem, not a license to abandon what makes the output a map.
2. **Source real data even when the direct path is blocked.** Check package registries and secondary sources before settling for "plausible."
3. **Build incrementally and confirm each step.** Layering isn't just presentation — it's what makes review catch gaps.
4. **Re-verify facts every time, not just the first time.** Precedent from an earlier layer is not evidence for a later one.
5. **Treat coordinate math as globally suspect on every structural change.** If the layout changes, recompute the whole coordinate set rather than patching the delta.
6. **Prefer found space over new structure.** A small addition doesn't always need a new column — check the map for room first.

Swap "companies on a map" for any other layered, fact-checked, spatially-anchored artifact and this holds. The trail is marked; hike it.
