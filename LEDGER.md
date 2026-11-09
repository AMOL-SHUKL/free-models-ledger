# Free Models Ledger

Routing table for zero-price model access on this device. Two quota domains exist: OpenCode Console, capped per account, and the OmniRoute gateway, capped per upstream. The unit of cost is one request, not one token.

**State 2026-11-09. OpenCode 2.2.6, gateway 3.8.52. 3 routed Console accounts, 14 gateway connections, 12 Console free models, 7 gateway providers usable. LongCat 2.5 Free is the temporary orchestrator at 100 requests/day; workstation review 2026-11-23. No key material in `opencode.json`.**

Sections: 1 Metric, 2 Domains, 3 Shortlist, 4 Gateway, 5 Budget, 6 Uncapped and expiry, 7 Archive, 8 Console inventory, 9 Cards, 10 Devices, 11 Drift, 12 Privacy, 13 Policy, 14 Provenance, 15 Stats.

An agent that only needs to route work reads sections 2, 3, and 6. Stop there.

## 1. The metric

One request is one agent step. A turn that reads, edits, and stops costs one step. A repair loop costs another.

Measured 2026-11-09 with `opencode stats`: 126 prompts, 6.8k steps, 29 sessions, 28 subagents, 14 active days. That is about 54 steps per prompt.

Three consequences drive the picks.

- A model that finishes in 20 steps beats one that finishes in 60, at equal quality.
- Delegation is not free. A subagent spends its own steps from the same domain.
- A published benchmark is a proxy. A measured step count replaces it.

Rule: pick the model with the fewest expected steps, in the cheapest domain that can run it. Quality is a gate, not a tiebreak. A failed task costs more than a slow one.

## 2. Domains

A domain is a quota bucket with its own auth. Steps in one domain do not drain another.

| Domain | Ref prefix | Auth | Cap | State | Order |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Console College | `opencode-college/` | API key (`auth.json`) | high throughput | primary orchestrator | 1 |
| Console Personal | `opencode-personal/` | API key (`auth.json`) | high throughput | failover 1 | 1 |
| Console Ronaldo | `opencode-ronaldo/` | API key (`auth.json`) | high throughput | failover 2 | 1 |
| OpenCode OAuth | `opencode/` | OAuth (`/connectors`) | ~200 steps/seat | user entry, fallback | 2 |
| Gateway Codex | `omni-codex/` | OAuth, 2 accounts | plan quota, expires 2026-11-19 | verified | 3 |
| Gateway OpenRouter | `omni-openrouter/` | API key | free-tier rate limits | verified | 3 |
| Gateway Mistral | `omni-mistral/` | API key | free tier | verified | 3 |
| Gateway Cline | `omni-cline/` | OAuth | plan quota, expires 2026-11-09 | partial | 3 |
| Gateway Gemini | `omni-gemini/` | API key | free tier, gemma only | partial | 3 |
| Gateway Groq | `omni-groq/` | API key | free tier, rejects agent prompt | partial | 3 |
| Local | `ornith/` | dummy key | none, server closed | offline | - |

LongCat 2.5 Free is not a separate domain. It shares the Console auth and adds a model cap of 100 requests per day. The cap is shared across all four Console prefixes on this host. Rotating accounts does not reset it. Verified 2026-11-09: all four refs returned `Free usage limit reached`.

The three Console accounts provide the routed OpenCode surface. The API key streams (`opencode-college/`, `opencode-personal/`, `opencode-ronaldo/`) serve automated agent execution first. The OAuth stream (`opencode/`) serves user interactive sessions second. The gateway (`omni-*`) is last. LongCat orchestrates from the Console API-key stream. Subagents stay on the permanent Console free models.

Verified 2026-10-28: OAuth user seats and Console API keys drain separate quota buckets in the same workspace. Console College logged 536 steps in one day at zero cost (171 via OAuth user, 365 via API key). The LongCat cap is the exception: it is shared, not per bucket.

The gateway is the last resort. Spend a gateway step only when both Console streams are throttled or the class needs a gateway-only model. Exception: hard agentic work escalates to Codex terra. No `apiKey` is stored in `opencode.json`. The gateway does not enforce auth on chat completions, only on the models endpoint.

## 3. Shortlist

Eight entries. Each wins one task class in one domain. Anything absent is archive.

| Class | Ref | Domain | Why it wins | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| Orchestration, long-horizon automation | `opencode-college/longcat-2.5-free` | Console API | AutomationBench 65.1 against DeepSeek 54.8. 1M context, 262k output, text and image. Trails DeepSeek on repo-scale work | personal longcat, then `opencode-college/deepseek-v4.1-flash-free` |
| Hard agentic subagent, repo-scale | `opencode-college/deepseek-v4.1-flash-free` | Console API | Best permanent repo-scale executor. DeepSWE 74.2, SkillsBench 69.8 | personal, ronaldo, then `omni-codex/codex/gpt-5.6-terra` |
| Atomic edit, bulk | `opencode-college/deepseek-v4.1-flash-free` | Console API | Strong planner, 200k context, cheap bulk edits | `omni-mistral/mistral/codestral-latest` |
| Vision, large context | `opencode-college/nemotron-3.5-super-vl-free` | Console API | 1M input with images | `omni-openrouter/openrouter/nvidia/nemotron-3.5-super-vl:free` |
| Cheap background | `opencode-college/nemotron-3.5-lightning-free` | Console API | 2 s, 262k output, no tiers | `omni-openrouter/openrouter/poolside/laguna-xs-2.1:free` |
| Video input | `opencode-college/glm-5.3-flash-free` | Console API | Text, image, video input | `opencode-college/mimo-v2.6-flash-free` |
| Audio input | `opencode-college/mimo-v2.6-flash-free` | Console API | Only route with audio input | none |
| PDF input | `omni-codex/codex/gpt-5.6-luna` | gateway | Only route with pdf input | `omni-codex/codex/gpt-5.6-terra` |

The LongCat row replaces the Beam row for the promo window. Beam returns as the large-context default on 2026-11-23. LongCat orchestrates. Its measured position is narrow: it trails DeepSeek on DeepSWE, 64.6 against 74.2, and on SkillsBench, 63.3 against 69.8. It leads on office-workflow automation. Do not give it repo construction or bulk edits, and subagents must not inherit it.

The orchestrator selects the variant per task. The table names the model only.

Selection order: fewest steps, then cheapest domain, then lowest variance, then data policy.

Do not add a ninth entry without a measured class the eight cannot serve.

## 4. Gateway providers

Two sets exist. Fourteen connections are configured. Seven providers expose models. The sets overlap but do not match.

### 4.1 Configured connections, 14

| Connection | Auth | State | Evidence |
| :--- | :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | OAuth | working | `codex/gpt-5.6-terra` OK, 4 s. Expires 2026-11-19 |
| `codex` amolshukla.bcr21221 | OAuth | working | Second account, same window. Expires 2026-11-19 |
| `openrouter` GitHub | API key | working | 60 catalog entries, 12 refs wired |
| `mistral` Mistral | API key | working | 55 entries, 26 refs wired |
| `aihorde` AI Horde | none | working | 182 entries, 14 refs wired, anonymous queue |
| `cline` AMOL SHUKLA | OAuth | partial | 13 refs wired. `beam-free` returns 500. Key expires 2026-11-09 |
| `gemini` main | API key | partial | 42 entries, 18 refs wired. Gemma models work, branded models 404 |
| `groq` main | API key | partial | 15 entries, 8 refs wired. OpenCode gets 413, prompt too large |
| `duckduckgo-web` | none | active | No wired models. `omni-ddg` absent from CLI |
| `cloudflare-playground` | none | active | No wired models. Needs Playwright and Chromium |
| `theoldllm` | none | active | No wired models |
| `chipotle` | none | active | No wired models |
| `uncloseai` | none | active | No wired models |
| `felo-web` | none | unavailable | Connection disabled |

### 4.2 Catalog-only providers

None observed. `/v1/models` exposes 444 entries across the same seven providers. The catalog exposes 378 entries. No provider appears in the catalog without a connection.

### 4.3 Gateway notes

- `duckduckgo-web` is active but exposes no wired models. The `omni-ddg` prefix is absent from `opencode models`. Do not route there.
- `gpt-6.1-terra` is not listed on the gateway now. Codex carries `gpt-5.6-terra`, `gpt-5.6-luna`, and `gpt-6-luna`.
- `cline` returns 500 on some models and 402 on paid ones. Treat it as opportunistic, not a default. Its credential expires 2026-11-09.
- `aihorde` works anonymously through the `aihorde/` prefix. The `horde/` alias requires a key. An account key would raise queue priority, not add models.
- `groq` works at the gateway and fails inside OpenCode. The agent system prompt exceeds the model limit. Do not route agent sessions there.
- `gemini` serves gemma models only. `gemma-4-31b-it` is confirmed working.
- The gateway does not enforce auth on chat completions. It does on `/v1/models`. No `apiKey` is stored in `opencode.json`, and provider routes still work.
- Codex account priority is 1 for ronaldoamol and 2 for amolshukla. The second drains only after the first throttles.
- `providers test-all` on 2026-11-09: 4 API-key connections valid, 8 OAuth or no-auth connections skipped, 2 inactive or unavailable.
- The gateway reports 444 model entries. The catalog reports 378. OpenCode wires 96. Entry count is not availability.

## 5. Step budget

Console steps are the scarce resource. Spend Console API keys first, then OAuth, then the gateway.

| Share | Steps | Class | Ref |
| :--- | ---: | :--- | :--- |
| 10% | 20 | orchestration and review | longcat, then deepseek |
| 40% | 80 | hard agentic subagents | deepseek, then codex terra |
| 25% | 50 | bulk and atomic edits | deepseek, codestral |
| 15% | 30 | large context and vision | longcat, beam, nemotron super vl |
| 10% | 20 | multimodal documents | glm, mimo, codex luna |

Rules:

- Try the Console API key first. Move to OAuth when the API keys throttle. Use the gateway when both Console streams fail or the class needs a gateway-only model.
- Rotate Console prefixes on throttle, not on model quality. LongCat is the exception. Its 100/day cap is shared, so a limit hit is final for the day.
- Give LongCat the orchestration share only. Subagents run DeepSeek or GLM.
- Keep 20% of the Console budget for the last quarter of the day.
- Use one orchestrator per task. Nested subagents multiply steps in one domain.

## 6. Promotions, uncapped domains and expiry

A domain outside the 200/day cap changes the ranking for the classes it covers. Today that is the gateway: Codex plan quota, OpenRouter free tier, Mistral free tier.

A promotion is not an uncapped domain. It has a start, an end, a cap, and a fallback. LongCat 2.5 Free is the only live window.

| Window | Ref | Start | End | Cap | State |
| :--- | :--- | :--- | :--- | :--- | :--- |
| LongCat 2.5 free | `opencode/longcat-2.5-free` and the three Console API prefixes | 2026-11-02 | 2026-11-23 (review) | 100 requests/day, shared across all four prefixes | live; quota-blocked at the 2026-11-09 15:45 UTC probe |

Rules:

- Use LongCat for orchestration, review, and long-horizon automation. Do not spend it on bulk edits.
- On `Free usage limit reached`, switch to DeepSeek. Do not retry another prefix; the cap is shared.
- The reset boundary is not published. Recheck with a one-line probe at the start of each session; until it answers, treat the day as spent.
- The 2026-11-23 end date is a workstation review date, not a vendor date. The vendor states only that the model is free for a limited time.
- On 2026-11-23, remove LongCat from the shortlist. Beam and DeepSeek are the permanent defaults.
- A promotion never blocks a session. The root default stays DeepSeek.

Expiry is not a promotion. It is the date the stored credential stops working. `omniroute providers status` reports it per connection.

| Connection | Expires | Effect on expiry |
| :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | 2026-11-19 | Route fails until re-auth |
| `codex` amolshukla.bcr21221 | 2026-11-19 | Second account, same date |
| `cline` AMOL SHUKLA | 2026-11-09 | Route fails until re-auth |

Rules:

- In an uncapped domain rank by capability, not steps.
- Keep specialists for pdf, audio, and 1M vision. Codex covers pdf only.
- Record an expiry date for every connection that has one. A lapsed credential looks like a 500 or a timeout.
- On expiry, fall through to section 3. Never leave a dead route as default.
- Re-auth is a user action. The ledger only records the date.

## 7. Archive, do not route

| Ref or connection | Why excluded |
| :--- | :--- |
| `opencode/big-pickle` | Free, but text only, closed weights, no unique modality. Dominated by deepseek |
| `opencode/nemotron-3-ultra-free` | Absent from the Console free catalog on 2026-11-09 |
| `opencode/longcat-2.5-preview-free` | Retired preview. Replaced by `longcat-2.5-free` on 2026-11-02 |
| `opencode/crouton-alpha-free` | Unmeasured stealth model. 1M text, image, video, PDF. Candidate for PDF only after a measured run |
| `opencode/iceman-free` | Unmeasured stealth model. Text only, 262k. No measured class |
| `opencode/inkling-2-small-free` | Unmeasured. 1M text and image, but data may train. No measured class |
| `opencode/*` paid refs | 72 non-free refs. No place in a free ledger |
| `omni-groq/*` | Prompt size rejected inside OpenCode. Gateway-only use |
| `omni-gemini/gemini-*` | Branded models 404. Gemma models remain usable |
| `omni-cline/cline/reflection/beam-free` | HTTP 500 at the provider |
| `omni-cline/*` paid refs | 402 Insufficient credits |
| `duckduckgo-web` | Active connection, no wired models |
| `cloudflare-playground`, `theoldllm`, `chipotle`, `uncloseai` | Active connections, no wired models |
| `felo-web` | Unavailable |
| `ornith/ornith-1.5-9b` | Configured local route, server on 8080 is closed |
| `aihorde/*` | Works anonymously, but the shared queue is slower than Console for background work |

The Console list added four free refs since 2026-10-30. LongCat enters the shortlist. The other three stay here until a measured step count beats a listed pick.

A model leaves archive only with a measured step count that beats a listed pick. Record it in section 14.

## 8. Console inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Knowledge | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/big-pickle | 200,000 | 32,000 | text | yes | - | 2025-10-17 | 2025-01 | none |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | 2026-06-30 | reflection/beam-1.0-501b-a23b |
| opencode/crouton-alpha-free | 1,048,576 | 270,000 | text, image, video, pdf | yes | minimal, low, medium, high, xhigh, max | 2026-11-03 | - | none |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | toggle, low, high, max | 2026-09-10 | 2025-05 | deepseek/deepseek-v4.1-flash |
| opencode/flint-40b-free | 262,144 | 65,536 | text | yes | low, medium, high, xhigh, max | 2026-10-21 | 2026-06 | recursive/flint |
| opencode/glm-5.3-flash-free | 200,000 | 32,000 | text, image, video | yes | low, high, max | 2026-08-26 | - | zhipuai/glm-5.3-flash |
| opencode/iceman-free | 262,144 | 131,072 | text | yes | - | 2026-11-06 | - | none |
| opencode/inkling-2-small-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, max | 2026-11-05 | - | thinkingmachines/inkling-2-small |
| opencode/longcat-2.5-free | 1,000,000 | 262,144 | text, image | yes | toggle | 2026-11-02 | - | meituan/longcat-2.5 |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | - | xiaomi/mimo-v2.6-flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | 2025-09 | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | - | nvidia/nemotron-3.5-super-vl-120b-a12b |
<!-- END AUTO:inventory -->

Each free model is also reachable under `opencode-college/`, `opencode-personal/`, and `opencode-ronaldo/`. Those 36 duplicate refs add no distinct model. They add quota buckets. The LongCat cap is shared across them.

## 9. Cards

<!-- BEGIN AUTO:cards -->
### opencode/longcat-2.5-free
- Name: LongCat 2.5 Free. Status: active, promotional to 2026-11-23. Released: 2026-11-02.
- Description: Meituan's multimodal reasoning model for long-horizon coding and agent tasks. 1.6T total parameters, about 48B active.
- Context: 1,000,000 in, 262,144 out. Input text and image.
- Reasoning: yes, toggle. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: zero-retention, not used for training. Hosted in China.
- Route: primary orchestrator. 100 requests/day shared across all Console prefixes. Fall back to deepseek on limit.
- Benchmark: vendor table. Terminal Bench v2.1 88.4, v4.0 38.5, AutomationBench 65.1, DeepSWE 64.6, SkillsBench 63.3. Trails DeepSeek on DeepSWE by 9.6 and SkillsBench by 6.5.

### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-09-10. Knowledge cutoff: 2025-05.
- Description: DeepSeek V4.1 Flash model for reasoning and agentic coding.
- Context: 200,000 in, 32,000 out. Input text and image.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Prompts and responses are not used for training.
- Benchmark: DeepSWE v1.1 74.2, SkillsBench 69.8, #1 of 35 (Vals).
- Route: hard agentic subagent execution. The permanent default when LongCat is unavailable. Keep context tight.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts model with about 2.5B active parameters for local coding agents.
- Context: 262,144 in, 65,536 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Open weights: yes.
- Policy: may-use-during-free-period. Data may train the model. Retained 30 days.
- Route: atomic edits and bulk mechanical work.

### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts reasoning model with 23B active parameters for coding and agentic workflows.
- Context: 1,048,576 in, 131,072 out. Input text.
- Reasoning: yes, effort low to max. Tools: yes. Open weights: yes.
- Policy: retained 30 days for safety. Regional blocklist applies.
- Route: large repositories and long documents.

### opencode/glm-5.3-flash-free
- Name: GLM-5.3-Flash Free. Status: active. Released: 2026-08-26.
- Description: Native multimodal GLM model for efficient coding and long-horizon agent tasks.
- Context: 200,000 in, 32,000 out. Input text, image, video.
- Reasoning: yes, effort low, high, max. Tools: yes. Open weights: yes.
- Policy: zero-retention. Not used for training.
- Route: video input. Text and image also work.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15.
- Description: NVIDIA vision-language reasoning model for text, image, and video understanding and agentic tool use.
- Context: 1,048,576 in, 131,072 out. Input text and image.
- Reasoning: yes, effort none to xhigh. Tools: yes. Open weights: yes.
- Policy: nvidia-trial-terms. Logged for security and product improvement.
- Route: large-context vision. Smaller images go to deepseek.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22.
- Description: MiMo Flash model for multimodal coding agents and long-context automation.
- Context: 200,000 in, 32,000 out. Input text, image, audio, video.
- Reasoning: yes, no effort control. Tools: yes. Open weights: yes.
- Policy: may-use-during-free-period.
- Route: sole audio route.

### opencode/nemotron-3.5-lightning-free
- Name: Nemotron 3.5 Lightning Free. Status: active. Released: 2026-08-11. Knowledge cutoff: 2025-09.
- Description: Fast NVIDIA Nemotron MoE for reliable agentic tasks across enterprise workloads.
- Context: 262,144 in, 262,144 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Open weights: yes.
- Policy: nvidia-trial-terms.
- Route: cheap background scans and summaries.

### opencode/big-pickle
- Name: Big Pickle. Status: active. Released: 2025-10-17. Knowledge cutoff: 2025-01.
- Description: Reasoning model for deliberate analysis, multi-step problem solving, and tool use.
- Context: 200,000 in, 32,000 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Open weights: no.
- Policy: not stated.
- Route: archived. Text only, dominated by deepseek.
<!-- END AUTO:cards -->

Cards cover the shortlist. Big Pickle keeps full specs in section 8, so restoration needs no research.

## 10. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-11-09

- OpenCode 2.2.6 at `/usr/bin/opencode`. Config `/root/.config/opencode/opencode.json`.
- Credentials: three Console API keys in `auth.json`. The gateway key lives in the gateway env file. Values never recorded.
- Configured prefixes: three Console API key accounts, seven `omni-*` gateway providers, one OAuth `opencode/` stream, and one local `ornith/` route.
- `opencode.json` stores no key material except the dummy 5-character `ornith` key. That server is closed.
- Default model: `opencode/deepseek-v4.1-flash-free`. Orchestrator agent `orchestrator` is defined as `opencode-college/longcat-2.5-free`; subagents are `opencode-college/deepseek-v4.1-flash-free`. The running service started before the edit. Restart it to activate the agent.
- Small model: `omni-codex/codex/gpt-5.6-luna`. Title agent: `omni-groq/groq/qwen/qwen3.8-27b`. Compaction agent: `omni-codex/codex/gpt-5.6-luna`.
- Gateway: OmniRoute 3.8.52, `http://localhost:20128/v1`, healthy, 14 requests in 24 h, uptime 1,948 s at probe.
- Gateway connections: 14 configured, 13 active, 1 unavailable.
- Local servers on 11434, 1234, 8000, 8080: none listening.
- Steps this period: 6.8k over 126 prompts, 29 sessions, 28 subagents, 14 active days.

## 11. Drift watchlist

- Console free list grew from 8 to 12 models between 2026-10-30 and 2026-11-09. New: `crouton-alpha-free`, `iceman-free`, `inkling-2-small-free`, `longcat-2.5-free`. Only LongCat is promoted.
- The Console catalog reports LongCat as 0 context and text only. models.dev and the official docs report 1M context and text plus image. Trust models.dev and the docs for capability. Trust the Console catalog for price.
- LongCat carries a 100 request/day cap. It is shared across all four Console prefixes. Rotating accounts does not reset it.
- The LongCat reset boundary is not published. Until a probe answers, treat the day as spent.
- The `orchestrator` agent is in `opencode.json` and resolves on a fresh config load. The running service started 2026-11-09 20:41 IST, before the edit. It appears after a service restart.
- Gateway model entries dropped from 494 to 444. Catalog entries dropped from 394 to 378. Wired refs dropped from 113 to 96.
- Codex expiry moved from 2026-11-08 to 2026-11-19. `cline` expires 2026-11-09.
- `nemotron-3-ultra-free` is still listed in the docs free set but absent from the Console catalog.
- Console free list and gateway connection list change independently. Check both.
- `duckduckgo-web` is active but exposes no wired models. Recheck after each gateway upgrade.
- `gemini` model map is stale. Verify a branded model before routing.
- Catalog and CLI disagree. Catalog gateway 378, `/v1/models` 444, CLI 96 gateway refs.
- Console catalog counts 205 entries across three accounts, paid models included. The CLI exposes 120 Console refs: 84 OAuth and 36 API key.
- Use the CLI as the routing source of truth for Console refs. Use `/v1/models` for gateway refs.

## 12. Privacy and data handling

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| LongCat 2.5 Free | not used | zero retention; hosted in China |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| GLM-5.3-Flash Free | not used | zero retention |
| Beam Free | not used | 30 days, regional blocklist |
| Crouton Alpha Free | not used | 30 days, stealth, regional blocklist |
| MiMo-V2.6-Flash Free, Flint 40B Free | may improve during the free period | Flint keeps 30 days |
| Iceman Free | may improve the model | retained during the free period |
| Inkling 2 Small Free | may train | retained, regional blocklist |
| Nemotron 3.5 Lightning, 3.5 Super VL | nvidia trial terms | no personal or confidential data |
| Codex via gateway | governed by the OpenAI account plan | check before use |
| OpenRouter, Mistral, Cline, Gemini, Groq | governed by each upstream account | check before use |
| Big Pickle | may improve the model during the free period | not stated |

## 13. Policy

- R1 step efficiency: fewest expected steps for the class wins.
- R2 quality gate: a model must clear the class minimum. A cheap failure is not a saving.
- R3 domain economy: Console API key first, then OAuth, then gateway. Failover across the three Console accounts before any model downgrade.
- R4 capability fit: tools, reasoning control, modalities, context length.
- R5 data policy: zero retention before 30-day before may-train before trial terms.
- R6 review work uses a different model family than the author.
- R7 archive is inert. A model returns only with a measurement.
- R8 no key material in `opencode.json`. Keys live in `auth.json` or the gateway env file.
- R9 promotion overlay: a dated free window may take one class. It must name an end date, a cap, and a fallback. A promotion never blocks a session.
- R10 shared caps: rotate prefixes only on per-account caps. A model cap shared across prefixes does not fail over.
- R11 benchmark prior: a vendor table is directional. A competitor score is not a host measurement. Promote only on a step count from section 14.
- Override: a dated measurement with a stated method replaces a pick.

## 14. Provenance and measurements

Checked 2026-11-09.

- Host: `opencode --version` 2.2.6, `opencode stats` 126 prompts and 6.8k steps, `opencode auth list` 3 Console accounts, `opencode models` 217 lines.
- Gateway: OmniRoute 3.8.52, `providers status` 14 connections, `/v1/models` 444 entries, catalog 378 entries, CLI 96 gateway refs. Health: 14 requests in 24 h, uptime 1,948 s.
- Catalog file: 205 Console entries across three accounts, 378 gateway entries. CLI: 217 refs. Console API-key prefixes expose 12 free refs each.
- Console probes 2026-11-09: 11 of 12 free models answered. Times: big-pickle 2 s, beam 2 s, deepseek 7 s, flint 2 s, glm 3 s, mimo 7 s, lightning 3 s, super-vl 2 s, crouton 2 s, iceman 3 s, inkling 3 s.
- LongCat failure: all four Console prefixes returned `Free usage limit reached` before and during the probe. The snapshot records one prefix.
- Gateway probes 2026-11-09: `codex/gpt-5.6-luna` 10 s, `codex/gpt-5.6-terra` 3 s. Both answered OK.
- Prior gateway probes 2026-10-30: `openrouter/nvidia/nemotron-3.5-super-vl:free` 10 s, `mistral/codestral-latest` 7 s, `openrouter/poolside/laguna-xs-2.1:free` 2 s.
- Gateway failure: `openrouter/google/gemma-4-31b-it:free` returned no reply in 75 s on 2026-10-30. Removed as a fallback.
- Direct credential probe to the Console endpoint returns 403. Quota is not readable outside OpenCode.
- Benchmark sources: LongCat vendor table checked 2026-11-09. DeepSeek SkillsBench 69.8 and Terminal-Bench 2.1 74.53 from Vals, DeepSWE 74.2 from third-party reports. Competitor scores in the LongCat table are partly third-party or self-run. No independent LongCat sweep exists yet. Treat all launch tables as directional.
- Prior probe: eight Console free refs answered on 2026-10-30.

Measurement protocol for a pick change:

1. Fix one task with a stated acceptance check.
2. Run the candidate and the incumbent on the same task.
3. Record prompt, steps, tool calls, domain, and pass or fail.
4. Promote on fewer steps with a pass. Demote on failure, not on slowness.

## 15. Stats

<!-- BEGIN AUTO:stats -->
- Console refs: 120 across 4 prefixes. 84 OAuth, 36 API key.
- Console free models: 12 distinct.
- Console free refs: 48, from 12 models across 4 prefixes.
- Routed Console accounts: 3. Anonymous `opencode/` is OAuth fallback.
- Gateway connections: 14 configured. Working 5, partial 3, no models 5, unavailable 1.
- Gateway providers usable: 7. Fully working 4, partial 3.
- Gateway model entries: 444 live, 378 catalog, 96 wired.
- Shortlist: 8 entries across 2 domains.
- LongCat 2.5: temporary orchestrator, 100 requests/day shared across 4 prefixes, quota-blocked 2026-11-09.
- Key material in `opencode.json`: none, except the dummy local `ornith` key.
- Next credential expiry: cline 2026-11-09, codex 2026-11-19.
- Host: OpenCode 2.2.6, OmniRoute 3.8.52, Arch Linux ARM64 chroot, `arch-btw`.
- Collected: 2026-11-09.
<!-- END AUTO:stats -->
