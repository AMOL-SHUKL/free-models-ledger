# free-models-ledger

Dated routing ledger for zero-price model access on Amol's devices. Synced through GitHub so any device can pull, run a collection, and push an update.

The ledger is not a catalog dump. It covers two quota domains: OpenCode Console, capped per account, and the OmniRoute gateway, capped per upstream. It ranks models by expected steps to done, keeps an eight-entry shortlist, and holds everything else in an inert archive.

## Layout

| File | Role |
| :--- | :--- |
| LEDGER.md | The report. Opens with the metric, the shortlist, quota domains, budget, and promo state. Then archive, inventory, cards, devices, drift, privacy, policy, provenance, stats. |
| UPDATE.md | The update protocol. Any agent, any device, follows it top to bottom. |
| CHANGELOG.md | Append-only run history, newest first. |
| snapshot.json | Latest machine-readable collection. Source of truth for diffs. |
| history/ | One snapshot per run, named YYYY-MM-DD-device.snapshot.json. |

## Definitions

- Free model: a catalog entry whose effective price is zero on every tier: input, output, cache read, cache write.
- Stable key: providerID/modelID. Every table, card, diff, and changelog line uses it.
- Evidence tags: C host catalog, D models.dev, O OpenCode docs, A auth list, H host probe.
- Step: one agent request against one model. The unit of quota cost.
- Pareto shortlist: the eight models that cover every task class. The only models that get routed.
- Archive: every other ref, listed with a reason. Never routed.
- Domain: one quota bucket with its own auth. Console account or gateway upstream. Steps do not cross domains.
- Connection: one authenticated upstream entry in the gateway. Connections and model providers are separate sets.
- Promo mode: an uncapped window. Switches ranking from step economy to raw capability.

## Rules

- Never store secret values. Environment variable names only.
- History files are append-only. Never rewrite or delete them.
- Machine-owned regions in LEDGER.md sit between BEGIN AUTO and END AUTO comment markers. Edit narrative only outside them.
- A shortlist entry needs a task class no other entry serves. Otherwise it belongs in archive.
- Never rebuild the free set from the models tool alone. The CLI lists refs the tool omits.
- Collect Console and gateway independently. A working Console account does not imply a working gateway connection.
- Never route a gateway ref before a live chat call confirms it. Model count is not availability.

## Daily update

Read UPDATE.md and follow it. In short: git pull, probe the host, pull the catalog through the models tool, cross-check the CLI count, enrich from models.dev, diff against the newest history file, write a new snapshot, regenerate the AUTO regions in LEDGER.md, append CHANGELOG.md, commit, push.
