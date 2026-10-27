# Free Models Ledger

Routing table for zero-price model access on this device. Two quota domains exist: OpenCode Console, capped per account, and the OmniRoute gateway, capped per upstream. The unit of cost is one request, not one token.

**State 2026-10-27. OpenCode 2.2.0, gateway 3.8.52. 3 routed Console accounts, 17 gateway connections, 9 Console free models, 7 gateway providers usable. No key material in `opencode.json`.**

Sections: 1 Metric, 2 Domains, 3 Shortlist, 4 Gateway, 5 Budget, 6 Uncapped and expiry, 7 Archive, 8 Console inventory, 9 Cards, 10 Devices, 11 Drift, 12 Privacy, 13 Policy, 14 Provenance, 15 Stats.

An agent that only needs to route work reads sections 2, 3, and 6. Stop there.

## 1. The metric

One request is one agent step. A turn that reads, edits, and stops costs one step. A repair loop costs another.

Measured 2026-10-27 with `opencode stats`: 39 prompts, 1.4k steps, 12 subagents, 5 active days. That is about 36 steps per prompt.

Three consequences drive the picks.

- A model that finishes in 20 steps beats one that finishes in 60, at equal quality.
- Delegation is not free. A subagent spends its own steps from the same domain.
- A published benchmark is a proxy. A measured step count replaces it.

Rule: pick the model with the fewest expected steps, in the cheapest domain that can run it. Quality is a gate, not a tiebreak. A failed task costs more than a slow one.

## 2. Domains

A domain is a quota bucket with its own auth. Steps in one domain do not drain another.

| Domain | Ref prefix | Auth | Cap | Counts against 200/day | State |
| :--- | :--- | :--- | :--- | :--- | :--- |
| OpenCode anonymous | `opencode/` | none stored | undisclosed free tier | assumed yes | fallback only, not routed |
| Console College | `opencode-college/` | API key | about 200 steps | yes | primary |
| Console Personal | `opencode-personal/` | API key | about 200 steps | yes | failover 1 |
| Console Ronaldo | `opencode-ronaldo/` | API key | about 200 steps | yes | failover 2 |
| Gateway Codex | `omni-codex/` | OAuth, 2 accounts | plan quota, token expires 2026-10-29 | no | verified |
| Gateway OpenRouter | `omni-openrouter/` | API key | free-tier rate limits | no | verified |
| Gateway Mistral | `omni-mistral/` | API key | free tier | no | verified |
| Gateway Cline | `omni-cline/` | OAuth | plan quota, key expires 2026-10-27 | no | partial |
| Gateway DuckDuckGo | `omni-ddg/` | none | unofficial, unmeasured | no | verified |
| Gateway Gemini | `omni-gemini/` | API key | free tier, gemma only | no | partial |
| Gateway Groq | `omni-groq/` | API key | free tier, rejects agent prompt | no | partial |
| Local | none installed | not applicable | not applicable | not applicable | none |

The three Console accounts are the routed OpenCode surface. The bare `opencode/` prefix is an anonymous fallback. It stays documented but is not used while an account key is valid.

Unverified: whether the three Console accounts drain separate buckets. A direct credential probe returns HTTP 403, so quota cannot be read outside OpenCode. Treat the split as likely, not proven.

The gateway is the relief valve. Spend a Console step only when no gateway domain can do the work. No `apiKey` is stored in `opencode.json`. The gateway does not enforce auth on chat completions, only on the models endpoint.

## 3. Shortlist

Eight entries. Each wins one task class in one domain. Anything absent is archive.

| Class | Ref | Domain | Why it wins | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| Hard agentic, multi-file | `omni-codex/codex/gpt-5.6-terra` | gateway | Strongest route, 3.3 s, no cap. Default for hard work | `opencode-college/deepseek-v4.1-flash-free#max` |
| Hard agentic, capped | `opencode-college/deepseek-v4.1-flash-free#max` | Console | DeepSWE 77.0, Apex 72.1. Best measured planner on the capped side | personal, then ronaldo |
| Large context over 200k | `opencode-college/beam-free#high` | Console | Only strong 1M route, 131k output | `omni-openrouter/openrouter/nvidia/nemotron-3-super-120b-a12b:free` |
| Atomic edit, bulk | `opencode-college/flint-40b-free#medium` | Console | SWE-bench Pro 89.7%, 2.5B active. Edits in place | `omni-mistral/mistral/codestral-latest` |
| Vision, large context | `omni-openrouter/openrouter/nvidia/nemotron-3.5-super-vl:free` | gateway | 1M input with images, uncapped | `opencode-college/nemotron-3.5-super-vl-free#high` |
| Cheap bulk and background | `omni-ddg/duckduckgo-web/gpt-5.6-luna` | gateway | No auth, no cap, 7 s. Good for scans and summaries | `opencode-college/mimo-v2.6-flash-free` |
| Document input, pdf and video | `opencode-college/glm-5.3-flash-free` | Console | Only route with pdf and video input | none |
| Audio input | `opencode-college/mimo-v2.6-flash-free` | Console | Only route with audio input | none |

Selection order: fewest steps, then cheapest domain, then lowest variance, then data policy.

Do not add a ninth entry without a measured class the eight cannot serve.

## 4. Gateway providers

Two sets exist. Seventeen connections are configured. Fourteen providers expose models. The sets overlap but do not match.

### 4.1 Configured connections, 17

| Connection | Auth | State | Evidence |
| :--- | :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | OAuth | working | `codex/gpt-5.6-terra` OK, 3.3 s. Expires 2026-10-29 |
| `codex` amolshukla.bcr21221 | OAuth | working | Second account, same window. Expires 2026-10-29 |
| `openrouter` GitHub | API key | working | `nemotron-3.5-super-vl:free` OK, 1.3 s |
| `mistral` Mistral | API key | working | `codestral-latest` OK, 5.7 s |
| `duckduckgo-web` | none | working | `gpt-5.6-luna` OK, 7.5 s |
| `cline` AMOL SHUKLA | OAuth | partial | `nemotron-3.5-lightning-free` OK. `beam-free` returns 500. Key expires 2026-10-27 |
| `gemini` main | API key | partial | `gemma-4-31b-it` OK. Gemini-branded models return 404 |
| `groq` main | API key | partial | Gateway OK, 0.4 s. OpenCode gets 413, prompt too large |
| `opencode-zen` College | API key | inert | Orphaned: CLI lists it, API returns 404, no models |
| `opencode-zen` Personal | API key | inert | Orphaned: CLI lists it, API returns 404, no models |
| `opencode-zen` Ronaldo | API key | inert | Orphaned: CLI lists it, API returns 404, no models |
| `aihorde` | none | broken | No key in any env file or in the connection record |
| `cloudflare-playground` | none | broken | Needs Playwright and Chromium |
| `chipotle` | none | unverified | No models exposed |
| `theoldllm` | none | unverified | No models exposed |
| `uncloseai` | none | unverified | No models exposed |
| `felo-web` | none | inactive | Connection disabled |

### 4.2 Catalog-only providers, no connection

These five appear in `/v1/models` with model counts but have no configured connection. Each fails on a missing local dependency.

| Provider | Models | Failure |
| :--- | ---: | :--- |
| `auggie` | 28 | Auggie CLI not installed |
| `codex-app-server` | 52 | Transport not configured |
| `devin-cli-agentic` | 127 | `DEVIN_AGENTIC_HOME` invalid |
| `zcode` | 14 | Binary missing |
| `veoaifree-web` | 4 | Timeout at 50 s |

### 4.3 Gateway notes

- The three `opencode-zen` connections are orphaned. `providers list` and their `/api/providers` record disagree: the API returns `Connection not found`, so they cannot be disabled through the API. The CLI has no remove command. They expose no models, and auto-routing is disabled, so no request can reach them. They are harmless but unremovable without a database edit.
- `gpt-6.1-terra` is listed on the gateway through Cline, not Codex. Every variant returns 402 Insufficient credits. Codex carries `gpt-5.6-terra` and `gpt-6-luna` only.
- `cline` returns 500 on some models and 402 on paid ones. Treat it as opportunistic, not a default.
- `aihorde` has no key in `/root/.omniroute/.env`, `/home/amol/.omniroute/.env`, or the connection record. Add the key in the gateway, or leave it broken.
- `groq` works at the gateway and fails inside OpenCode. The agent system prompt exceeds the model limit. Do not route agent sessions there.
- `gemini` serves gemma models only. `gemma-4-31b-it` is confirmed working.
- The gateway does not enforce auth on chat completions. It does on `/v1/models`. No `apiKey` is stored in `opencode.json`, and provider routes still work.
- Codex account priority is 1 for ronaldoamol and 2 for amolshukla. The second drains only after the first throttles.
- The gateway reports 1850 model entries. OpenCode wires 154 of them. Entry count is not availability.

## 5. Step budget

Console steps are the scarce resource. Gateway steps are not. Drain the gateway first.

| Share | Steps | Class | Ref |
| :--- | ---: | :--- | :--- |
| 45% | 90 | hard agentic and review | Codex, then deepseek on fallback |
| 25% | 50 | bulk and atomic edits | flint, codestral, ddg |
| 20% | 40 | large context and vision | beam, nemotron super-vl |
| 10% | 20 | multimodal documents | glm, mimo |

Rules:

- Try gateway first. Move to Console only when the gateway class is missing or fails.
- Rotate Console prefixes on throttle, not on model quality.
- Keep 20% of the Console budget for the last quarter of the day.
- Use one orchestrator per task. Nested subagents multiply steps in one domain.

## 6. Uncapped domains and expiry

A domain outside the 200/day cap changes the ranking for the classes it covers. Today that is the gateway: Codex plan quota, OpenRouter free tier, Mistral free tier, DuckDuckGo.

No promotional unlimited provider is live today. If one appears, it joins this section and becomes the default for every class it can serve.

Expiry is not a promotion. It is the date the stored credential stops working. `omniroute providers status` reports it per connection.

| Connection | Expires | Effect on expiry |
| :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | 2026-10-29 | Route fails until re-auth |
| `codex` amolshukla.bcr21221 | 2026-10-29 | Second account, same date |
| `cline` AMOL SHUKLA | 2026-10-27 | Route fails until re-auth |

Rules:

- In an uncapped domain rank by capability, not steps.
- Keep specialists for pdf, audio, and 1M vision. Codex covers none of them.
- Record an expiry date for every connection that has one. A lapsed credential looks like a 500 or a timeout.
- On expiry, fall through to section 3. Never leave a dead route as default.
- Re-auth is a user action. The ledger only records the date.

## 7. Archive, do not route

| Ref or connection | Why excluded |
| :--- | :--- |
| `opencode/big-pickle` | 2025 route, text only, closed weights, no unique modality |
| `opencode/nemotron-3.5-lightning-free` | Dominated by flint. Keep only for output over 131k |
| `opencode/nemotron-3-ultra-free` | Dominated by beam and deepseek. No unique modality |
| `omni-groq/*` | Prompt size rejected inside OpenCode. Gateway-only use |
| `omni-gemini/gemini-*` | Branded models 404. Gemma models remain usable |
| `omni-cline/openai/gpt-6.1-terra*` | Listed, but every variant returns 402 Insufficient credits |
| `omni-cline/*/beam-free` | HTTP 500 at the provider |
| `opencode/*` | Anonymous fallback. Not routed while an account key is valid |
| 9 unusable connections and 5 catalog-only providers | Section 4 states the reason for each |
| 30 paid `opencode/` refs | Non-zero price. No place in a free ledger |

A model leaves archive only with a measured step count that beats a listed pick. Record it in section 14.

## 8. Console inventory

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

Each free model is also reachable under `opencode-college/`, `opencode-personal/`, and `opencode-ronaldo/`. Those 24 duplicate refs add no distinct model. They add quota buckets.

## 9. Cards

<!-- BEGIN AUTO:cards -->
### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-09-10. Knowledge cutoff: 2025-05.
- Description: Reasoning and agentic coding model. DeepSWE v1.1 77.0, Apex Bench 72.1.
- Context: 200,000 in, 32,000 out. Input text and image.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Prompts and responses are not used for training.
- Route: hard agentic work on the capped side. Keep context tight.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts with about 2.5B active parameters.
- Context: 262,144 in, 65,536 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Open weights: yes.
- Policy: may-use-during-free-period. Data may train the model. Retained 30 days.
- Route: atomic edits and bulk mechanical work.

### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts with 23B active parameters for coding and agentic work.
- Context: 1,048,576 in, 131,072 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Open weights: yes.
- Policy: retained 30 days for safety. Regional blocklist applies.
- Route: large repositories and long documents.

### opencode/glm-5.3-flash-free
- Name: GLM-5.3-Flash Free. Status: active. Released: 2026-08-26.
- Description: Native multimodal model for efficient coding and long-horizon agent tasks.
- Context: 200,000 in, 32,000 out. Input text, image, video, pdf.
- Reasoning: yes, effort low, high, max. Tools: yes. Open weights: yes.
- Policy: zero-retention. Not used for training.
- Route: sole pdf and video route.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15.
- Description: Vision-language reasoning model for text, image, and video understanding.
- Context: 1,048,576 in, 131,072 out. Input text and image.
- Reasoning: yes, effort none to xhigh. Tools: yes. Open weights: yes.
- Policy: nvidia-trial-terms. Logged for security and product improvement.
- Route: large-context vision. Smaller images go to glm.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22.
- Description: Flash model for multimodal coding agents and long-context automation.
- Context: 200,000 in, 32,000 out. Input text, image, audio, video.
- Reasoning: yes, no effort control. Tools: yes. Open weights: yes.
- Policy: may-use-during-free-period.
- Route: sole audio route.
<!-- END AUTO:cards -->

Cards cover the shortlist. The three archived Console models keep full specs in section 8, so restoration needs no research.

## 10. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-10-27

- OpenCode 2.2.0 at `/usr/bin/opencode`. Config `/root/.config/opencode/opencode.json`.
- Credentials: three Console API keys and one gateway key in `auth.json`. Values never recorded.
- Configured prefixes: three routed Console accounts, seven `omni-*` gateway providers, and the anonymous `opencode/` fallback.
- `opencode.json` stores no key material. All seven `apiKey` entries were removed on 2026-10-27 and every route still answers.
- Default model: `opencode-college/deepseek-v4.1-flash-free`. Small model: `omni-codex/codex/gpt-5.6-luna`.
- Gateway: OmniRoute 3.8.52, `http://localhost:20128/v1`, healthy, 14 requests in 24 h.
- Gateway connections: 17 configured, 16 active, 1 unavailable.
- Local servers on 11434, 1234, 8000: none listening.
- Steps this period: 1.4k over 39 prompts, 7 sessions, 12 subagents.

## 11. Drift watchlist

- Console free list and gateway connection list change independently. Check both.
- `opencode-zen` gateway connections return 404 while native Console routes work. Recheck after each gateway upgrade.
- `cline` and both `codex` accounts carry expiry dates. A silent expiry looks like a 500 or a timeout.
- `gemini` model map is stale. Verify a branded model before routing.
- Catalog tool and CLI disagree by four refs. See section 14.
- Use the CLI as the routing source of truth for Console refs. Use `/v1/models` for gateway refs.

## 12. Privacy and data handling

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| GLM-5.3-Flash Free | not used | zero retention |
| Beam Free | not used | 30 days, regional blocklist |
| MiMo-V2.6-Flash Free, Flint 40B Free | may train during the free period | Flint keeps 30 days |
| Nemotron Ultra, 3.5 Lightning, 3.5 Super VL | nvidia trial terms | no personal or confidential data |
| Codex via gateway | governed by the OpenAI account plan | check before use |
| OpenRouter, Mistral, Cline, Gemini, Groq | governed by each upstream account | check before use |
| DuckDuckGo web | unofficial web route | no private data |
| Big Pickle | not stated | not stated |

## 13. Policy

- R1 step efficiency: fewest expected steps for the class wins.
- R2 quality gate: a model must clear the class minimum. A cheap failure is not a saving.
- R3 domain economy: gateway before Console. Failover across the three Console accounts before any model downgrade.
- R4 capability fit: tools, reasoning control, modalities, context length.
- R5 data policy: zero retention before 30-day before may-train before trial terms.
- R6 review work uses a different model family than the author.
- R7 archive is inert. A model returns only with a measurement.
- R8 no key material in `opencode.json`. Keys live in `auth.json` or the gateway env file.
- Override: a dated measurement with a stated method replaces a pick.

## 14. Provenance and measurements

Checked 2026-10-27.

- Host: `opencode --version` 2.2.0, `opencode stats`, `opencode auth list`, `opencode models` 217 lines.
- Gateway: OmniRoute 3.8.52, `providers list`, `providers status`, `providers test-all`, `/v1/models` 1850 entries.
- Catalog tool: 213 refs. CLI: 217 refs. Delta four. The tool omits three free Console refs.
- Console probes: all four prefixes answered `beam-free` on 2026-10-27.
- Gateway probes: one live chat call per connection, then targeted retries where a model-level error appeared.
- Direct credential probe to the Console endpoint returns 403. Quota is not readable outside OpenCode.
- Prior probe: eight Console free refs answered on 2026-10-23. `big-pickle` has never been probed.

Measurement protocol for a pick change:

1. Fix one task with a stated acceptance check.
2. Run the candidate and the incumbent on the same task.
3. Record prompt, steps, tool calls, domain, and pass or fail.
4. Promote on fewer steps with a pass. Demote on failure, not on slowness.

## 15. Stats

<!-- BEGIN AUTO:stats -->
- Console refs: 217 across 10 configured prefixes.
- Console free models: 9 distinct.
- Console free refs: 33, from 9 models across 4 prefixes.
- Routed Console accounts: 3. Anonymous `opencode/` is fallback only.
- Gateway connections: 17 configured. Working 5, partial 3, inert 3, broken 2, unverified 3, inactive 1.
- Gateway catalog-only providers: 5, all blocked on a local dependency.
- Gateway providers usable: 7. Fully working 4, partial 3.
- Gateway model entries: 1850. Wired into OpenCode: 154.
- Shortlist: 8 entries across 2 domains.
- Key material in `opencode.json`: none.
- Next credential expiry: cline 2026-10-27, codex 2026-10-29.
- Host: OpenCode 2.2.0, OmniRoute 3.8.52, Arch Linux ARM64 chroot, `arch-btw`.
- Collected: 2026-10-27.
<!-- END AUTO:stats -->
