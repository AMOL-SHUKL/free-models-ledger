# free-models-ledger

Dated, declarative ledger of every zero-price model reachable from OpenCode on Amol's devices. Synced through GitHub so any device can pull, run a collection, and push an update.

## Layout

| File | Role |
| :--- | :--- |
| LEDGER.md | The report. Opens with Top 3 picks per work category, then inventory, cards, devices, drift, privacy, policy, provenance, stats. |
| UPDATE.md | The update protocol. Any agent, any device, follows it top to bottom. |
| CHANGELOG.md | Append-only run history, newest first. |
| snapshot.json | Latest machine-readable collection. Source of truth for diffs. |
| history/ | One snapshot per run, named YYYY-MM-DD-device.snapshot.json. |

## Definitions

- Free model: a catalog entry whose effective price is zero on every tier: input, output, cache read, cache write.
- Stable key: providerID/modelID. Every table, card, diff, and changelog line uses it.
- Evidence tags: C host catalog, D models.dev, O OpenCode docs, A auth list, H host probe.

## Daily update

Read UPDATE.md and follow it. In short: git pull, probe the host, pull the catalog through the models tool, enrich from models.dev, diff against the newest history file, write a new snapshot, regenerate the AUTO regions in LEDGER.md, append CHANGELOG.md, commit, push.

## Rules

- Never store secret values. Environment variable names only.
- History files are append-only. Never rewrite or delete them.
- Machine-owned regions in LEDGER.md sit between BEGIN AUTO and END AUTO comment markers. Edit narrative only outside them.
