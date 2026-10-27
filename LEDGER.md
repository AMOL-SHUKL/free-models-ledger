# Free Models Ledger

Routing table for zero-price models on this device. The unit of cost is one request, not one token. Each opencode Console account allows about 200 requests per day. Model choice sets requests per task, so rank by steps to done.

**State 2026-10-27. Mode: budgeted. OpenCode 2.2.0. 217 refs, 10 providers, 9 free models, 4 Console prefixes.**

Sections: 1 Metric, 2 Shortlist, 3 Quota domains, 4 Budget, 5 Promo mode, 6 Archive, 7 Inventory, 8 Cards, 9 Devices, 10 Drift, 11 Privacy, 12 Policy, 13 Provenance, 14 Stats.

An agent that only needs to route work reads sections 2 and 3. Stop there.

## 1. The metric

One request is one agent step. A turn that reads, edits, and stops costs one step. A repair loop costs another step.

Measured 2026-10-27 with `opencode stats`: 39 prompts, 1.4k steps, 12 subagents, 5 active days. That is about 36 steps per prompt.

Three consequences drive every pick below.

- A model that finishes in 20 steps beats one that finishes in 60, at equal quality.
- Delegation is not free. A subagent spends its own steps from the same account.
- A published benchmark is a proxy. A measured step count replaces it.

Rule: pick the model with the fewest expected steps for the task class. Treat quality as a gate, not a tiebreak. A failed task costs more than a slow one.

## 2. Pareto shortlist

Six models cover every task class on this device. Route here first. Anything absent from this table is archive, and archive is never routed.

| Class | Ref | Step profile | Why it wins | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| Hard agentic, multi-file | `opencode/deepseek-v4.1-flash-free#max` | Lowest steps on hard work | DeepSWE 77.0, Apex 72.1. Best measured planner. Fewest repair loops | next account, same model |
| Atomic edit, bulk mechanical | `opencode/flint-40b-free#medium` | Fast turns, high first-pass rate | SWE-bench Pro 89.7%, 2.5B active. Edits in place, no rewrite | next account, same model |
| Large context over 200k | `opencode/beam-free#high` | Steady on long input | Only strong 1M route. 131k output | next account, same model |
| Document input, pdf and video | `opencode/glm-5.3-flash-free` | Single pass on documents | Only free route with pdf and video input | next account, same model |
| Large-context vision | `opencode/nemotron-3.5-super-vl-free#high` | Single pass on large images | 1M input with image support | next account, same model |
| Audio input | `opencode/mimo-v2.6-flash-free` | Single pass on audio | Only free route with audio input | next account, same model |

Selection order inside a class:

1. Lowest expected steps.
2. Lowest step variance. Prefer the model that does not need a retry.
3. Data policy. Zero retention before trial terms.

Do not add a seventh model without a measured class that the six cannot serve.

## 3. Quota domains

The daily cap applies per Console account, not per device. Four Console prefixes are configured, so the same model is reachable from four separate buckets.

| Domain | Ref prefix | Cap | Counts against opencode | Failover |
| :--- | :--- | :--- | :--- | :--- |
| Console shared | `opencode/` | about 200 steps | yes | next account |
| Console account 1 | `opencode-college/` | about 200 steps | separate bucket | next account |
| Console account 2 | `opencode-personal/` | about 200 steps | separate bucket | next account |
| Console account 3 | `opencode-ronaldo/` | about 200 steps | separate bucket | next model in section 2 |
| Gateway | `omni-*` | upstream quota | no | provider specific |
| Local | none installed | not applicable | not applicable | none |

Failover order: `opencode`, then `opencode-college`, then `opencode-personal`, then `opencode-ronaldo`.

Two properties make this useful.

- The model ref is identical across prefixes. Switching account keeps the model, so quality does not drop.
- The buckets drain independently. A throttled route is a routing error, not a reason to accept a weaker model.

Status: the per-account cap is an assumption. Neither the catalog nor the CLI exposes a quota counter. Confirm it with one probe per prefix before trusting the failover.

Unresolved: `opencode auth list` prints College, Github, and Personal. The mapping of Github to `opencode-ronaldo` is inferred, not verified.

## 4. Step budget

One account per day holds about five substantial tasks at 36 steps each. Spend by share, not by impulse.

| Share | Steps | Class | Ref |
| :--- | ---: | :--- | :--- |
| 60% | 120 | hard agentic and review | `deepseek-v4.1-flash-free#max` |
| 25% | 50 | bulk and atomic edits | `flint-40b-free#medium` |
| 10% | 20 | large context | `beam-free#high` |
| 5% | 10 | multimodal | `glm-5.3-flash-free`, `nemotron-3.5-super-vl-free#high`, `mimo-v2.6-flash-free` |

Rules:

- Keep 20% for the last quarter of the day. Do not exhaust a bucket before noon.
- Send file search and bulk reads to the gateway. Gateway steps do not drain a capped bucket.
- Use one orchestrator per task. Nested subagents multiply steps against one bucket.
- Rotate accounts on throttle, not on model quality. Never downgrade the model to save a step.

## 5. Promo mode

A promotional provider grants unlimited or high-limit use for a window. When one is live, the step budget stops binding and the ranking changes.

Declared state. Update this table, not prose.

| Field | Value |
| :--- | :--- |
| Mode | budgeted |
| Promo provider | none |
| Window start | none |
| Window end | none |
| Evidence | none |
| Revert to | section 2 shortlist |

Detection signals:

- `opencode auth list` shows a new credential or a changed tier.
- The Console plan page shows a promotional window.
- A route keeps answering past the 200 step mark.

Switch rule: in promo mode rank by capability only. The promo model becomes the default for every class where it is not clearly weaker. Keep the specialists for pdf, audio, and 1M vision.

Exit rule: record the end date before enabling promo mode. On expiry revert to section 2. A dead promo must never stay default.

Guard: promo routes often allow training on submitted data. Check row 11 before sending private code.

## 6. Archive, do not route

Excluded models stay listed so a future run does not re-add them by accident. No agent routes to this section.

| Ref | Why excluded |
| :--- | :--- |
| `opencode/big-pickle` | 2025 route, text only, closed weights, no unique modality. 200k input, 32k output |
| `opencode/nemotron-3.5-lightning-free` | Dominated by flint on coding. Keep only if one response must exceed 131k output |
| `opencode/nemotron-3-ultra-free` | Dominated by beam and deepseek. Older, no structured output, no unique modality |
| `omni-*` refs, 154 total | Price unmeasured, upstream quota only, not part of the capped budget |
| Paid `opencode/` refs, 30 total | Non-zero price. No place in a free ledger |

A model leaves archive only with a measured step count that beats a listed pick. Record the measurement in section 13.

## 7. Inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Policy | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | retained-30-days | reflection/beam-1.0-501b-a23b |
| opencode/big-pickle | 200,000 | 32,000 | text | yes | - | 2025-10-17 | not stated | big-pickle |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | low, high, max | 2026-09-10 | zero-retention | deepseek/deepseek-v4.1-flash |
| opencode/flint-40b-free | 262,144 | 65,536 | text | yes | low, medium, high, xhigh, max | 2026-10-21 | may-use-during-free-period | recursive/flint |
| opencode/glm-5.3-flash-free | 200,000 | 32,000 | text, image, video, pdf | yes | low, high, max | 2026-08-26 | zero-retention | zhipuai/glm-5.3-flash |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | may-use-during-free-period | xiaomi/mimo-v2.6-flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | nvidia-trial-terms | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | nvidia-trial-terms | nvidia/nemotron-3.5-super-vl-120b-a12b |
| opencode/nemotron-3-ultra-free | 1,000,000 | 128,000 | text | yes | - | 2026-06-04 | nvidia-trial-terms | nvidia/nemotron-3-ultra-550b-a55b |
<!-- END AUTO:inventory -->

Each of the nine free models above is also available under `opencode-college/`, `opencode-personal/`, and `opencode-ronaldo/`. Those 24 duplicate refs add no distinct model. They add quota buckets.

## 8. Model cards

<!-- BEGIN AUTO:cards -->
### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-09-10. Knowledge cutoff: 2025-05.
- Description: Reasoning and agentic coding model. DeepSWE v1.1 77.0, Apex Bench 72.1.
- Context: 200,000 in, 32,000 out. Input text and image.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Prompts and responses are not used for training.
- Route: primary for hard agentic work. Keep context tight. Delegate edits.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts with about 2.5B active parameters.
- Context: 262,144 in, 65,536 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: may-use-during-free-period. Data may train the model. Retained 30 days.
- Route: atomic edits and bulk mechanical work. Not a fallback for hard reasoning.

### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts with 23B active parameters for coding and agentic work.
- Context: 1,048,576 in, 131,072 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: retained 30 days for safety. Regional blocklist applies.
- Route: the only strong 1M class route. Use for large repositories and long documents.

### opencode/glm-5.3-flash-free
- Name: GLM-5.3-Flash Free. Status: active. Released: 2026-08-26.
- Description: Native multimodal model for efficient coding and long-horizon agent tasks.
- Context: 200,000 in, 32,000 out. Input text, image, video, pdf.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Not used for training.
- Route: sole pdf and video route. Also a sound mixed document and code driver.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15.
- Description: Vision-language reasoning model for text, image, and video understanding.
- Context: 1,048,576 in, 131,072 out. Input text and image.
- Reasoning: yes, effort none to xhigh. Tools: yes. Open weights: yes.
- Policy: nvidia-trial-terms. Logged for security and product improvement. No personal data.
- Route: large-context vision. Smaller images go to glm.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22.
- Description: Flash model for multimodal coding agents and long-context automation.
- Context: 200,000 in, 32,000 out. Input text, image, audio, video.
- Reasoning: yes, no effort control. Tools: yes. Open weights: yes.
- Policy: may-use-during-free-period.
- Route: sole audio route.
<!-- END AUTO:cards -->

Cards cover the shortlist only. The three archived free models keep full specs in the inventory table, so restoration needs no research.

`nemotron-3.5-lightning-free` and `nemotron-3-ultra-free` are archived for dominance, not for failure. Either returns on a measured step win.

## 9. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-10-27

- OpenCode 2.2.0 at `/usr/bin/opencode`. Config source `/root/.config/opencode/opencode.json`.
- Credentials: three Console accounts stored, College, Github, Personal. Values are never recorded here.
- Configured providers: `opencode`, three Console account prefixes, and seven `omni-*` gateway prefixes.
- Default model in config: `opencode/deepseek-v4.1-flash-free`. Small model: `omni-codex/codex/gpt-5.6-luna`.
- Gateway: `omniroute` on `http://localhost:20128/v1`. Answers, unmeasured price.
- Local servers on 11434, 1234, 8000: none listening.
- Steps consumed this period: 1.4k over 39 prompts, 7 sessions, 12 subagents.

## 10. Drift watchlist

- Free models absent from this catalog but documented upstream: exo-free, mimo-v2.5-free, longcat-2.5-preview-free, ll-alpha-free, muse-spark-1.3-contributor-free, jev-1.13-free.
- `opencode` paid list carries 30 non-free refs. Ignore them.
- The gateway lists 154 refs with empty cost arrays. Empty cost is not proof of zero price.
- Catalog tool and CLI disagree by four refs. See section 13.
- Watch rules: check the Console free list daily. Remove a model only after two runs absent. Use the CLI as the routing source of truth.

## 11. Privacy and data handling

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| GLM-5.3-Flash Free | not used | zero retention |
| Beam Free | not used | 30 days for safety, regional blocklist |
| MiMo-V2.6-Flash Free, Flint 40B Free | may train during the free period | Flint keeps 30 days |
| Nemotron Ultra, 3.5 Lightning, 3.5 Super VL | nvidia trial terms, logged, not linked to identity | no personal or confidential data |
| Big Pickle | not stated | not stated |
| Promo routes | check before use | check before use |

Beam blocklist: Angola, Belarus, Cambodia, China including Hong Kong and Macau, Cuba, Democratic Republic of the Congo, Equatorial Guinea, Iran, Iraq, Laos, Libya, Nigeria, North Korea, Russia, Syria, Turkmenistan, Uzbekistan, Venezuela, Crimea, Donetsk and Luhansk.

## 12. Ranking policy

- R1 step efficiency: fewest expected steps for the class wins.
- R2 quality gate: a model must clear the class minimum. A cheap failure is not a saving.
- R3 quota domain: prefer the bucket that still holds steps. Never downgrade the model to stay in one bucket.
- R4 capability fit: tools, reasoning control, modalities, context length.
- R5 data policy: zero retention before 30-day before may-train before trial terms.
- R6 review work uses a different model family than the author.
- R7 archive is inert. A model returns only with a measurement.
- Override: a dated measurement with a stated method replaces a pick in section 2.

## 13. Provenance and sources

Checked 2026-10-27.

- Host: `opencode --version` 2.2.0, `opencode stats`, `opencode auth list`, `opencode models` 217 lines.
- Catalog tool: 213 refs. CLI: 217 refs. Delta is four refs.
- The CLI is authoritative for routing, because it lists what `opencode run` resolves.
- Tool-only census misses `glm-5.3-flash-free`, `nemotron-3.5-lightning-free`, and `nemotron-3-ultra-free`. A rebuild from the tool alone deletes three models.
- Enrichment: `https://models.dev/api.json`, sha256 recorded in section 14.
- Prior probe: eight free refs answered one turn on 2026-10-23. `big-pickle` has never been probed.
- Evidence tags: C host catalog, D models.dev, O OpenCode docs, A auth list, H host probe.

Measurement protocol for a pick change:

1. Fix one task with a stated acceptance check.
2. Run the candidate and the incumbent on the same task.
3. Record prompt, steps, tool calls, and pass or fail.
4. Promote on fewer steps with a pass. Demote on failure, not on slowness.

## 14. Stats

<!-- BEGIN AUTO:stats -->
- Catalog: 217 refs across 10 configured prefixes.
- Free models: 9 distinct.
- Free refs: 33, from 9 models across 4 Console prefixes.
- `opencode/`: 39 refs, 9 free.
- Console account prefixes: 8 each, all free.
- `omni-*`: 154 refs, price unmeasured.
- Archive: 3 free models, 154 gateway refs, 30 paid refs.
- Shortlist: 6 entries.
- Mode: budgeted. No promo live.
- Host: OpenCode 2.2.0, Arch Linux ARM64 chroot, `arch-btw`.
- Collected: 2026-10-27, unprobed revision. Prior probe run 2026-10-23.
- models.dev snapshot sha256: 470452beddc8610ffb429fc14a0332e500bb9a6e1662075d6fee7bbe919cd690.
<!-- END AUTO:stats -->
