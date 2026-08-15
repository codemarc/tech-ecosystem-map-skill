# Contributing

Thanks for helping extend the map, or improve the skill itself.

## Ways to help

1. **Report a wrong fact** — a mislocated company, a stale HQ, an acquisition the map doesn't reflect yet
2. **Add region support** — help make the geography-sourcing recipe work smoothly for a new metro/state
3. **Improve the skill** — `tech-ecosystem-map/SKILL.md` workflow, verification checklist, or layout system

## Reporting a wrong fact

Open an issue with the correction template. Say what's wrong, what's right, and a source. The skill's own rule is to verify with a live search before adding anything — a correction should meet the same bar.

## Improving the skill

`tech-ecosystem-map/SKILL.md` is the workflow. The three load-bearing pieces are the real-geography sourcing method, the layout/offset math for sidebars and connector lines, and the rescan-before-adding discipline — see [`tech-ecosystem-map/references/braintrailz.md`](./tech-ecosystem-map/references/braintrailz.md) for why before proposing changes to those.

If you're changing the layout system, test it against a case with at least two sidebars on opposite sides of the map — that's where the offset math bugs actually show up.

## PRs

- One fact correction or one change per PR
- If you changed `tech-ecosystem-map/`, note it so the packaged `tech-ecosystem-map.skill` can be rebuilt
- Cite a source for any factual claim you're adding or changing

## Rebuilding the packaged skill

```bash
zip -r tech-ecosystem-map.skill tech-ecosystem-map -x "*.DS_Store"
```
