# The Tech Ecosystem Map Skill

> "The map is not the territory."
> — Alfred Korzybski

An Agent Skill for Claude, Codex, Grok, and Muse that builds real-geography, layered infographic maps of a region's tech, AI, and capital ecosystem — an actual coastline, not a stylized diagram — with sidebar cards linked to each company and connected to its real map location.

A [Braintrailz](https://braintrailz.com) composition by [codemarc](https://github.com/codemarc).

## The idea

A "tech map" is usually a diagram wearing a map's clothes: zones, boxes, connector lines, no real geography underneath. This skill insists on the territory, not just a map-shaped stand-in for it — real coastline data, real neighborhoods, real company websites.

It builds in layers. Start with the geography alone (coastline, roads, airports, reference cities), then add one category of companies at a time — AI, capital, fintech, whatever the region calls for — confirming each layer before moving to the next. And before any company lands on the map, it gets a live search to verify the claim being made: which neighborhood, headquarters or satellite office, independent company or subsidiary.

## What it does

- **Sources real coastline geometry** from Census cartographic boundary data (via the `us-atlas` npm package), not a hand-drawn approximation
- **Builds one layer at a time** — geography first, then each company category — checking in before adding the next
- **Verifies before adding** — every company gets a live web search for its current HQ/status before it goes on the map; unconfirmed candidates get dropped, not guessed
- **Corrects stale facts in place** — when a rescan finds something already on the map is wrong, it fixes it and notes the correction rather than leaving it standing
- **Uses found space over new structure** — a small layer gets in-map labels in an empty part of the map instead of a whole new sidebar column
- **Verifies structurally before showing you anything** — card/pin/connector-line counts match, tags are balanced, no two pins collide

## Install

**Claude.ai (web/desktop/mobile):**

1. Download [`tech-ecosystem-map.skill`](./tech-ecosystem-map.skill) from this repo (or grab it from Releases)
2. Drop it into a Claude chat and click **Save skill** — or add it via Settings → Capabilities → Skills

**Claude Code:**

```bash
git clone https://github.com/codemarc/tech-ecosystem-map-skill.git
mkdir -p ~/.claude/skills
cp -r tech-ecosystem-map-skill/tech-ecosystem-map ~/.claude/skills/tech-ecosystem-map
```

**Codex:**

```bash
git clone https://github.com/codemarc/tech-ecosystem-map-skill.git
mkdir -p ~/.codex/skills
cp -r tech-ecosystem-map-skill/tech-ecosystem-map ~/.codex/skills/tech-ecosystem-map
```

**Grok Build:**

```bash
git clone https://github.com/codemarc/tech-ecosystem-map-skill.git
mkdir -p ~/.grok/skills
cp -r tech-ecosystem-map-skill/tech-ecosystem-map ~/.grok/skills/tech-ecosystem-map
```

Grok also reads Claude Code skills with no extra setup — if `~/.claude/skills/tech-ecosystem-map` is already installed, you can skip the copy.

**Muse Code:**

```bash
git clone https://github.com/codemarc/tech-ecosystem-map-skill.git
mkdir -p ~/.config/muse/skills
cp -r tech-ecosystem-map-skill/tech-ecosystem-map ~/.config/muse/skills/tech-ecosystem-map
# or: muse skills install ./tech-ecosystem-map --scope user
```

## Update

```bash
cd /path/to/tech-ecosystem-map-skill
git pull
rm -rf ~/.claude/skills/tech-ecosystem-map
cp -r tech-ecosystem-map ~/.claude/skills/tech-ecosystem-map
```

Swap the target path for `~/.codex/skills`, `~/.grok/skills`, or `~/.config/muse/skills` as needed.

## Usage

Ask for a "tech map" or "AI map" of a city or region, or point at an existing map file and say "rescan," "refresh," or "add X to the map." No region specified defaults to refreshing the South Florida tech/AI/capital map this skill was built against (layers: AI companies, AI-adjacent infrastructure/capital, financial advisory/crypto/fintech, established pre-AI-era tech).

For a new region, expect it to start with geography only and build up one company layer at a time — that's deliberate, not a limitation.

## How it's built

The full composition methodology is in [`tech-ecosystem-map/references/braintrailz.md`](./tech-ecosystem-map/references/braintrailz.md) — why the first version got rejected for not actually being a map, why the sidebar/pin offset math is the riskiest edit in the whole system, and why a rescan beats trusting the last layer's precedent.

The short version:

1. **Solve constraints inside the form** — a single-screen limit is a layout problem, not a license to abandon the map for a diagram
2. **Source real data even when the direct path is blocked** — check package registries before settling for "plausible"
3. **Build incrementally and confirm each step** — layering is what makes review catch gaps
4. **Re-verify facts every time** — precedent from an earlier layer isn't evidence for a later one
5. **Treat coordinate math as globally suspect on every structural change** — recompute the whole set, don't patch the delta
6. **Prefer found space over new structure** — check the map for room before adding a column

The pattern is reusable. Swap "companies on a map" for any other layered, fact-checked, spatially-anchored artifact and the architecture holds.

## Report a correction

See [`.github/ISSUE_TEMPLATE/correction.md`](./.github/ISSUE_TEMPLATE/correction.md) — a wrong location, a stale HQ, an acquisition the map doesn't reflect. Cite a source.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md). By participating, you agree to the [Code of Conduct](./CODE_OF_CONDUCT.md).

## License

MIT — see [LICENSE](./LICENSE).
