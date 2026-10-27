# Changelog

Newest first. One entry per run. Format: `snapshot(YYYY-MM-DD): +added -removed changed`.

## 2026-10-27, routing(console): three accounts, no keys in config, expiry clarified

- `opencode.json` carries no key material. All seven `apiKey` entries removed and every route re-tested.
- Default model moved from the anonymous `opencode/` prefix to `opencode-college/deepseek-v4.1-flash-free`.
- Routing now uses the three authenticated Console accounts. Anonymous `opencode/` is fallback only.
- Section 6 rewritten. "Promo" replaced with uncapped domains plus credential expiry dates. Expiry is a token date, not a promotion.
- `gpt-6.1-terra` recorded as listed but unusable. Cline returns 402 Insufficient credits on every variant. Codex does not carry it.
- AI Horde status corrected: no key exists in any env file or in the connection record.
- `opencode-zen` x3 recorded as orphaned. The API cannot find them, the CLI cannot remove them, and auto-routing is disabled, so no request can reach them.
- Verified after the change: Codex, College, and Mistral all answered with no `apiKey` in config.

## 2026-10-27, docs(providers): add gateway domain and verified inventory

- Ledger now covers two quota domains. OpenCode Console, capped per account. OmniRoute gateway, capped per upstream.
- Gateway inventoried live. 17 connections configured, 14 providers expose models, 5 catalog-only providers, 1850 model entries.
- Verified working: codex (2 accounts), openrouter, mistral, duckduckgo-web. Partial: cline, gemini, groq.
- Inert: opencode-zen x3. Valid auth, no models, no default model, nothing routes to them.
- Four Console prefixes probed live and all answered. `opencode-ronaldo` is valid, not a stale entry.
- Promo mode enabled. Codex OAuth window ends 2026-10-29. Revert target is deepseek-v4.1-flash-free#max.
- Shortlist extended to eight entries across two domains. Codex becomes the uncapped default for hard work.
- Found: gateway `opencode-zen` returns 404 while native Console routes work. Same accounts, different transport.
- Found: Groq rejects the agent prompt with 413 inside OpenCode. Gateway-only use.
- Found: the Console API rejects direct credential use with 403, so quota is unreadable outside OpenCode.
- Corrected: the `omni-codex` key in `opencode.json` is an `{env:...}` reference, not an invalid literal key.

## 2026-10-27, docs(routing): rank by steps, split shortlist from archive

- Policy shift. The metric is now requests, not price. Every free model costs zero tokens, so the 200-per-day Console cap makes steps the scarce resource.
- Shortlist cut to six models, one per task class: deepseek-v4.1#max, flint-40b#medium, beam#high, glm-5.3, nemotron-3.5-super-vl#high, mimo-v2.6.
- Archive section added. `big-pickle` restored with a stated reason, no longer silently purged. `nemotron-3.5-lightning-free` and `nemotron-3-ultra-free` moved there as dominated.
- New section 3. The same model is reachable from four Console prefixes, so the cap is per account and failover preserves the model instead of downgrading it.
- New section 4. Daily step budget by share, 60/25/10/5.
- New section 5. Promo mode with a declared state table and a mandatory expiry date.
- Counts corrected: 217 refs, 10 prefixes, 9 free models, 33 free refs. Prior ledger claimed 458 models, 2 providers, 8 free.
- Recorded tool-versus-CLI delta, 213 against 217. The tool omits three free refs. CLI is authoritative for routing.
- No probe run this revision. Reachability still dates from 2026-10-23.

## 2026-10-23, docs(orchestration): designate DeepSeek V4.1 Flash as Primary Orchestrator

- Free set: 8 entries.
- Positioned opencode/deepseek-v4.1-flash-free as primary high-level orchestrator based on DeepSWE v1.1 (77.0) and Apex Bench (72.1) performance; delegates execution to subagents.
- Purged opencode/big-pickle completely from ledger inventory, cards, and snapshot.
- flint-40b-free designated for surgical atomic edits; removed from general multi-file fallback chain.
- Removed unverified personal quota and device telemetry notes.

## 2026-10-23, run 2026-10-23-arch-btw

- First run on arch-btw, Arch Linux ARM64 chroot, OpenCode 2.1.3. +1 -32. Added opencode/flint-40b-free.
- Free set rebuilt: 32 routes left because their providers are not configured on this device. opencode/minimax-m3.1-flash-free also left the Console free list and Console models table.
- Catalog 458 models, 2 providers: opencode 9 (9 free), omniroute local gateway 449 (0 proven free, empty cost arrays, recorded unmeasured).
- Changed: deepseek-v4.1-flash-free reasoning options gained a toggle; nemotron-3-ultra-free and nemotron-3.5-lightning-free knowledge cutoffs read 2025-09 in models.dev.
- Sources re-checked: Console models, Console Go, Models docs, models.dev. Device prose replaced. Windows device block retired as unverified.
- Ledger reset to current facts only. Top 3 picks, drift, privacy, ranking policy, devices, and narrative rewritten from the nine-model free set.
- Subagent picks use MiMo, Flint, and Nemotron Lightning. DeepSeek is manual use only.
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
