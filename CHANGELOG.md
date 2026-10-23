# Changelog

Newest first. One entry per run. Format: `snapshot(YYYY-MM-DD): +added -removed changed`.

## 2026-10-23, run 2026-10-23-arch-btw

- First run on arch-btw, Arch Linux ARM64 chroot, OpenCode 2.1.3. +1 -32. Added opencode/flint-40b-free.
- Free set rebuilt: 32 routes left because their providers are not configured on this device. opencode/minimax-m3.1-flash-free also left the Console free list and Console models table.
- Catalog 458 models, 2 providers: opencode 9 (9 free), omniroute local gateway 449 (0 proven free, empty cost arrays, recorded unmeasured).
- Changed: deepseek-v4.1-flash-free reasoning options gained a toggle; nemotron-3-ultra-free and nemotron-3.5-lightning-free knowledge cutoffs read 2025-09 in models.dev.
- Sources re-checked: Console models, Console Go, Models docs, models.dev. Device prose replaced. Windows device block retired as unverified.
- Ledger reset to current facts only. Top 3 picks, drift, privacy, ranking policy, devices, and narrative rewritten from the nine-model free set.
- Snapshot: history/2026-10-23-arch-btw.snapshot.json. models.dev sha256 470452beddc8610ffb429fc14a0332e500bb9a6e1662075d6fee7bbe919cd690.

## 2026-10-19, user preference override, no new snapshot

- R7 added to the ranking policy. Exclude Nemotron chat and reasoning routes from picks. User judgement: quality ranks below other free routes.
- Exception kept: opencode/nemotron-3.5-super-vl-free stays eligible for vision work.
- Exception kept: openrouter/nvidia/nemotron-3.5-content-safety:free stays eligible as a classifier.
- Picks changed. 1.2 long-context: Ultra replaced by MiniMax M3.1 Flash and LongCat. 1.4 subagents: Lightning replaced by MiMo. 1.5 review: Ultra replaced by Inkling and MiniMax.
- Agent config example changed from Ultra to glm-5.3-flash-free.
- Card notes added to opencode/nemotron-3.5-lightning-free and opencode/nemotron-3-ultra-free.
- Inventory, stats, and privacy sections unchanged. No catalog change.

## 2026-10-19, run 2026-10-19-laptop-f7fu0ssn

- +3 -1. Added opencode/minimax-m3.1-flash-free, opencode-go/minimax-m3.1-flash-free, openrouter/minimax/minimax-m3.1-flash:free. Removed opencode/space-bunny-free.
- Free total 40. Catalog 488. OpenCode 2.0.32. Provider display name changed to Github / OpenCode.
- Reveal: you report Space Bunny as MiniMax M3.1 Flash. Docs list MiniMax M3.1 Flash Free with zero retention and drop Space Bunny. Mapping recorded as reported, not officially confirmed.
- Go privacy table, checked today: MiniMax M3.1 Flash Free, not used for training, 0 days retention.
- Snapshot: history/2026-10-19-laptop-f7fu0ssn.snapshot.json. models.dev sha256 f6548e9a8644538a883c988fcfc981a1bb8ecf42d444e15d66228de3e3c4b52b.

## 2026-10-17, run 2026-10-17-laptop-f7fu0ssn

- Baseline. Catalog: 489 models, 5 providers. Free: 38 entries, opencode 10, openrouter 23, groq 3, opencode-go 2.
- Files: LEDGER.md, UPDATE.md, README.md, snapshot.json, history/2026-10-17-laptop-f7fu0ssn.snapshot.json.
- Drift seeded: ll-alpha-free, exo-free, mimo-v2.5-free, muse-spark-1.3-contributor-free, jev-1.13-free are documented free offers absent from this device catalog.
- Sources: opencode.ai/v2/docs pages console/models, console/go, models, agents, commands. models.dev/api.json. Local host probes.
