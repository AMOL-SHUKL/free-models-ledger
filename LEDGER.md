# Free Models Ledger

Routing table for zero-price model access on this device. Two quota domains exist: OpenCode Console, capped per account, and the OmniRoute gateway, capped per upstream. The unit of cost is one request, not one token.

**State 2026-11-16. OpenCode 2.2.7, gateway 3.8.52. 3 routed Console accounts, 14 gateway connections, 13 Console free models, 7 gateway providers usable. Hy4 flash Free is the promotional first pick to 2026-11-30. LongCat 2.5 Free orchestrates at 100 requests/day; workstation review 2026-11-23. DeepSeek V4.1 Flash Free stays the permanent root default. No key material in `opencode.json`.**

Sections: 1 Metric, 2 Domains, 3 Shortlist, 4 Gateway, 5 Budget, 6 Uncapped and expiry, 7 Archive, 8 Console inventory, 9 Cards, 10 Devices, 11 Drift, 12 Privacy, 13 Policy, 14 Provenance, 15 Stats.

An agent that only needs to route work reads sections 2, 3, and 6. Stop there.

## 1. The metric

One request is one agent step. A turn that reads, edits, and stops costs one step. A repair loop costs another.

Measured 2026-11-16 with `opencode stats`: 29 prompts, 1.4k steps, 10 sessions, 24 subagents, 7 active days. That is about 48 steps per prompt.

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

LongCat 2.5 Free is not a separate domain. It shares the Console auth. Its cap is 100 requests per day and that cap belongs to LongCat alone. A `Free usage limit reached` answer on DeepSeek or GLM is an upstream throttle that resets on its own schedule, not the LongCat counter. Rotating accounts does not reset LongCat.

Hy4 flash Free is not a separate domain either. It shares the Console auth and resolves on all four prefixes. Its cap is vendor-stated as near-unlimited, with no published number, and the window closes 2026-11-30. Near-unlimited is not unlimited: treat it as a promotional route, never as a permanent default.

The three Console accounts provide the routed OpenCode surface. The API key streams (`opencode-college/`, `opencode-personal/`, `opencode-ronaldo/`) serve automated agent execution first. The OAuth stream (`opencode/`) serves user interactive sessions second. The gateway (`omni-*`) is last. LongCat orchestrates from the Console API-key stream. Hy4 flash takes routine agentic work. Subagents stay on Console free models.

Verified 2026-10-28: OAuth user seats and Console API keys drain separate quota buckets in the same workspace. Console College logged 536 steps in one day at zero cost (171 via OAuth user, 365 via API key). The LongCat cap is the exception: it applies to one model, not to the Console stream.

The gateway is the last resort. Spend a gateway step only when both Console streams are throttled or the class needs a gateway-only model. Exception: hard agentic work escalates to Codex terra. No `apiKey` is stored in `opencode.json`. The gateway does not enforce auth on chat completions, only on the models endpoint.

## 3. Shortlist

Eight entries. Each wins one task class in one domain. Anything absent is archive.

| Class | Ref | Domain | Why it wins | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| Routine agentic, first pick | `opencode-college/hy4-flash-free` | Console API | Free to 2026-11-30 with near-unlimited usage. Median 6.7 min per task against DeepSeek 10.3 min. 1M context, text and image | `opencode-college/deepseek-v4.1-flash-free` |
| Orchestration, long-horizon automation | `opencode-college/longcat-2.5-free` | Console API | AutomationBench 65.1 on its vendor table. 1M context, 262k output, text and image | personal longcat, then `opencode-college/deepseek-v4.1-flash-free` |
| Hard agentic, repo-scale | `opencode-college/deepseek-v4.1-flash-free` | Console API | Terminal-Bench 4.0 42.3 against Hy4 flash 19.2. ProgramBench 29.1 against 16. DeepSWE 77.0. Highest task satisfaction, 3.09 | personal, ronaldo, then `omni-codex/codex/gpt-5.6-terra` |
| Multimodal coding, video input | `opencode-college/glm-5.3-flash-free` | Console API | Only free route with text, image, and video input | `opencode-college/mimo-v2.6-flash-free` |
| Vision, large context | `opencode-college/nemotron-3.5-super-vl-free` | Console API | 1M input with images, effort to xhigh | `omni-openrouter/openrouter/nvidia/nemotron-3.5-super-vl:free` |
| Cheap background | `opencode-college/nemotron-3.5-lightning-free` | Console API | 2 s, 262k output, no tiers | `omni-openrouter/openrouter/poolside/laguna-xs-2.1:free` |
| Audio input | `opencode-college/mimo-v2.6-flash-free` | Console API | Only route with audio input | none |
| PDF input | `omni-codex/codex/gpt-5.6-luna` | gateway | Only route with pdf input | `omni-codex/codex/gpt-5.6-terra` |

Hy4 flash takes the routine class from DeepSeek. It does not take the default. DeepSeek stays the root default because it is permanent, measured, and higher on the quality ceiling. Hy4 flash is a two-week promotional route. A default that expires is a stale config, so the promotion never touches the root setting.

The measured position of Hy4 flash is narrow and it is honest about it. Tencent recommends it for deep search, document processing, slides, simple frontend demos, and computer use. Tencent recommends the full Hy4, not the flash tier, for long-horizon software engineering. On hard agentic benchmarks it is not close to DeepSeek: Terminal-Bench 4.0 19.2 against 42.3, ProgramBench 16.0 against 29.1, AutomationBench 52.2 against 60.5.

### 3.1 Escalation ladder

The orchestrator starts every task on Hy4 flash and escalates on evidence, not on feel. The ladder is ordered by what each model provably wins.

1. Stay on `hy4-flash-free` for routine edits, search, summaries, document work, and any task that fits one pass. It is the Pareto point on cost and latency while the window is open.
2. Escalate to `deepseek-v4.1-flash-free` when the task spans a repository, needs a passing test loop, or has failed twice. Correctness outranks price.
3. Escalate to `longcat-2.5-free` when the task is long-horizon automation or needs 262k output in one reply. Its 100/day cap makes it an escalation route, not a default.
4. Escalate to `glm-5.3-flash-free` only for video input. It loses to DeepSeek on satisfaction, 2.53 against 3.09, at 3.1x the task cost on Tencent's blind test.
5. Never escalate for cost. Every Console free route costs zero on request count. Escalate for capability, modality, or context only.

Cross-vendor benchmark tables are not comparable. LongCat's AutomationBench 65.1 and Tencent's DeepSeek 60.5 come from different harnesses. Use them to rank within a table, never across two.

Selection order: fewest expected steps, then cheapest domain, then lowest variance, then data policy. The escalation ladder overrides the order on a capability gap.

Do not add a ninth entry without a measured class the eight cannot serve.

## 4. Gateway providers

Two sets exist. Fourteen connections are configured. Seven providers expose models. The sets overlap but do not match.

### 4.1 Configured connections, 14

| Connection | Auth | State | Evidence |
| :--- | :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | OAuth | working | `codex/gpt-5.6-terra` OK, 4 s. Expires 2026-11-19 |
| `codex` amolshukla.bcr21221 | OAuth | working | Second account, same window. Expires 2026-11-19 |
| `openrouter` GitHub | API key | working | 60 catalog entries, 14 refs wired |
| `mistral` Mistral | API key | working | 55 entries, 24 refs wired |
| `aihorde` AI Horde | none | working | 182 entries, 17 refs wired, anonymous queue |
| `cline` ronaldoamol.9699 | OAuth | partial | 15 refs wired. `beam-free` returns 500. Key expires 2026-11-16 |
| `gemini` main | API key | partial | 42 entries, 21 refs wired. Gemma models work, branded models 404 |
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
- The gateway reports 444 model entries. The catalog reports 378. OpenCode wires 112. Entry count is not availability.

## 5. Step budget

Console steps are the scarce resource. Spend Console API keys first, then OAuth, then the gateway.

| Share | Steps | Class | Ref |
| :--- | ---: | :--- | :--- |
| 15% | 30 | orchestration and review | longcat, then deepseek |
| 20% | 40 | hard agentic subagents | deepseek, then codex terra |
| 35% | 70 | routine agentic and bulk edits | hy4 flash, then deepseek |
| 15% | 30 | large context and vision | hy4 flash, super vl, beam |
| 10% | 20 | multimodal documents | glm, mimo, codex luna |
| 5% | 10 | background scans | lightning |

Rules:

- Try the Console API key first. Move to OAuth when the API keys throttle. Use the gateway when both Console streams fail or the class needs a gateway-only model.
- Start routine work on Hy4 flash. It carries the largest share while its window is open.
- On 2026-11-30 the Hy4 flash share returns to DeepSeek. No rule depends on the promotion staying live.
- Rotate Console prefixes on throttle, not on model quality. LongCat is the exception. Its 100/day cap is shared across prefixes, so a LongCat limit hit is final for the day. DeepSeek and GLM throttles reset and do not carry that cap.
- Give LongCat the orchestration share only. Subagents run Hy4 flash or DeepSeek.
- Keep 20% of the Console budget for the last quarter of the day.
- Use one orchestrator per task. Nested subagents multiply steps in one domain.

## 6. Promotions, uncapped domains and expiry

A domain outside the 200/day cap changes the ranking for the classes it covers. Today that is the gateway: Codex plan quota, OpenRouter free tier, Mistral free tier.

A promotion is not an uncapped domain. It has a start, an end, a cap, and a fallback. Two windows are live.

| Window | Ref | Start | End | Cap | State |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Hy4 flash free | `opencode-college/hy4-flash-free` and all four Console prefixes | 2026-11-16 | 2026-11-30 | near-unlimited, no published number | live; answered on all four prefixes at the 2026-11-16 probe |
| LongCat 2.5 free | `opencode-college/longcat-2.5-free` and all four Console prefixes | 2026-11-02 | 2026-11-23 (review) | 100 requests/day, shared across all four prefixes | live; cap reset, probes answered 2026-11-16 |

Rules:

- Hy4 flash takes routine agentic work. It does not take the root default. A default that expires is a stale config.
- Use LongCat for orchestration, review, and long-horizon automation. Do not spend it on bulk edits.
- On `Free usage limit reached`, switch to DeepSeek and keep working. The LongCat 100/day cap belongs to LongCat alone; a DeepSeek or GLM throttle resets on the provider schedule.
- The Hy4 flash cap is described as near-unlimited, not numeric. Never plan a batch on the assumption that it cannot throttle.
- The LongCat reset boundary is not published. Recheck with a one-line probe at the start of each session; until it answers, treat the day as spent.
- The 2026-11-23 LongCat end date is a workstation review date, not a vendor date. The vendor states only that the model is free for a limited time.
- On 2026-11-30, remove Hy4 flash from the shortlist. DeepSeek returns to the routine class. Tencent's own free WorkBuddy access and half-price TokenHub pricing close on the same date, so no extension is expected.
- On 2026-11-23, remove LongCat from the shortlist. Beam and DeepSeek are the permanent defaults.
- A promotion never blocks a session. The root default stays DeepSeek.

Expiry is not a promotion. It is the date the stored credential stops working. `omniroute providers status` reports it per connection.

| Connection | Expires | Effect on expiry |
| :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | 2026-11-19 | Route fails until re-auth |
| `codex` amolshukla.bcr21221 | 2026-11-19 | Second account, same date |
| `cline` ronaldoamol.9699 | 2026-11-16 | Route fails until re-auth. Expires today |

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
| `opencode/crouton-alpha-free` | Retired 2026-11-16. models.dev marks it deprecated and the CLI rejects it as unavailable |
| `opencode/minimax-m3.1-flash-free` | Unmeasured. 1M context, 512k output, text, image, video, open weights. Candidate only after a measured run against DeepSeek |
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

The Console list turned over since 2026-11-09. Crouton retired, Hy4 flash and MiniMax M3.1 Flash entered. Hy4 flash is promoted to the routine class on a dated window. Two refs sit here unmeasured.

A model leaves archive only with a measured step count that beats a listed pick. Record it in section 14.

## 8. Console inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Knowledge | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | 2026-06-30 | reflection/beam-1.0-501b-a23b |
| opencode/big-pickle | 200,000 | 32,000 | text | yes | - | 2025-10-17 | 2025-01 | none |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | low, high, max | 2026-10-17 | 2025-05 | deepseek/deepseek-v4.1-flash-1017 |
| opencode/flint-40b-free | 262,144 | 65,536 | text | yes | low, medium, high, xhigh, max | 2026-10-21 | 2026-06 | recursive/flint |
| opencode/glm-5.3-flash-free | 200,000 | 32,000 | text, image, video | yes | low, high, max | 2026-08-26 | - | zhipuai/glm-5.3-flash |
| opencode/hy4-flash-free | 1,048,576 | 65,536 | text, image | yes | none, high | 2026-11-16 | - | tencent/hy4-flash |
| opencode/iceman-free | 262,144 | 131,072 | text | yes | - | 2026-11-06 | - | none |
| opencode/inkling-2-small-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, max | 2026-11-05 | - | thinkingmachines/inkling-2-small |
| opencode/longcat-2.5-free | 1,000,000 | 262,144 | text, image | yes | toggle | 2026-11-02 | - | meituan/longcat-2.5 |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | - | xiaomi/mimo-v2.6-flash |
| opencode/minimax-m3.1-flash-free | 1,000,000 | 512,000 | text, image, video | yes | low, medium, high, xhigh, max | 2026-10-19 | - | minimax/MiniMax-M3.1-Flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | 2025-09 | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | - | nvidia/nemotron-3.5-super-vl-120b-a12b |
<!-- END AUTO:inventory -->

Each free model is also reachable under `opencode-college/`, `opencode-personal/`, and `opencode-ronaldo/`. Those 39 duplicate refs add no distinct model. They add quota buckets. The LongCat and Hy4 flash caps are shared across them.

`opencode models` lists Hy4 flash once, under `opencode/`. The Console catalog carries it under all four prefixes and every prefix answered a live probe on 2026-11-16. Trust the catalog and the probe over the CLI count for this ref.

## 9. Cards

<!-- BEGIN AUTO:cards -->
### opencode/hy4-flash-free
- Name: Hy4 flash Free. Status: active. Promotional window to 2026-11-30. Released: 2026-11-16.
- Description: Tencent's lower-cost Hy4 tier for everyday agent and productivity tasks, with image understanding and a 1M-token context window.
- Context: 1,048,576 in, 65,536 out. Input text, image.
- Reasoning: yes, effort none, high. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: zero-retention. OpenCode states ZDR for the free window.
- Route: Promotional first pick for routine agentic work to 2026-11-30. Near-unlimited usage, not unlimited. Falls back to DeepSeek on throttle or expiry.
- Benchmark: Tencent launch table. Terminal-Bench 4.0 19.2, ProgramBench 16.0, AutomationBench 52.2. Blind test over 259 tasks: satisfaction 2.79 of 4, median 6.7 min, 0.26 yuan per task, against DeepSeek 3.09 at 10.3 min. Below DeepSeek on every hard coding benchmark.
- Escalation: hand repo-scale, terminal, and multi-pass work to DeepSeek. Tencent recommends the full Hy4, not this tier, for long-horizon engineering.

### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-10-17. Knowledge cutoff: 2025-05.
- Description: October 17 update of DeepSeek V4.1 Flash with improved reasoning, coding, agentic tasks, and visual understanding
- Context: 200,000 in, 32,000 out. Input text, image.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Prompts and responses are not used for training.
- Route: Permanent root default and the hard agentic escalation step. Keep context tight.
- Benchmark: DeepSWE v1.1 77.0, Terminal-Bench 4.0 42.3, ProgramBench 29.1, AutomationBench 60.5, SkillsBench 69.8. Highest task satisfaction in Tencent's 259-task blind test, 3.09 of 4.

### opencode/longcat-2.5-free
- Name: LongCat 2.5 Free. Status: active. Promotional to 2026-11-23. Released: 2026-11-02.
- Description: Meituan's multimodal reasoning model for long-horizon coding and agent tasks, with visual understanding and a 1M-token context window
- Context: 1,000,000 in, 262,144 out. Input text, image.
- Reasoning: yes, toggle. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: zero-retention, not used for training. Hosted in China.
- Route: Primary orchestrator. 100 requests/day shared across all Console prefixes. Escalation route for long-horizon automation with 262k output.
- Benchmark: Vendor table. Terminal Bench v2.1 88.4, v4.0 38.5, AutomationBench 65.1, DeepSWE 64.6, SkillsBench 63.3. Trails DeepSeek on DeepSWE by 9.6 and SkillsBench by 6.5. Cross-table comparison with Tencent runs is directional only.
- Window: closes 2026-11-23 at the workstation review. Never the root default; a config that expires is stale.

### opencode/glm-5.3-flash-free
- Name: GLM-5.3-Flash Free. Status: active. Released: 2026-08-26.
- Description: Native multimodal GLM model for efficient coding and long-horizon agent tasks
- Context: 200,000 in, 32,000 out. Input text, image, video.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Not used for training.
- Route: Video input only. Its own satisfaction score, 2.53, trails DeepSeek at 3.09, and it costs 3.1x per task.
- Benchmark: Satisfaction 2.53 of 4, median 7.6 min, 0.81 yuan per task on Tencent's blind test. Keeps one unique lane: text, image, and video input.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15.
- Description: NVIDIA vision-language reasoning model for text, image, and video understanding and agentic tool use
- Context: 1,048,576 in, 131,072 out. Input text, image.
- Reasoning: yes, effort none, minimal, low, medium, high, xhigh. Tools: yes. Structured output: not stated. Open weights: yes.
- Policy: nvidia-trial-terms. Logged for security and product improvement.
- Route: Large-context vision. Smaller images go to Hy4 flash or DeepSeek.

### opencode/nemotron-3.5-lightning-free
- Name: Nemotron 3.5 Lightning Free. Status: active. Released: 2026-08-11. Knowledge cutoff: 2025-09.
- Description: Fast NVIDIA Nemotron MoE for reliable agentic tasks across enterprise workloads
- Context: 262,144 in, 262,144 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: nvidia-trial-terms.
- Route: Cheap background scans and summaries.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22.
- Description: MiMo Flash model for multimodal coding agents and long-context automation
- Context: 200,000 in, 32,000 out. Input text, image, audio, video.
- Reasoning: yes, no effort control. Tools: yes. Structured output: not stated. Open weights: yes.
- Policy: may-use-during-free-period.
- Route: Sole audio route.

### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts reasoning model with 23B active parameters for coding and agentic workflows
- Context: 1,048,576 in, 131,072 out. Input text.
- Reasoning: yes, effort low, medium, high, xhigh, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: retained 30 days for safety. Regional blocklist applies.
- Route: Large repositories and long documents. Off the shortlist while Hy4 flash and LongCat hold the 1M-context lanes.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts model with about 2.5B active parameters for local coding agents
- Context: 262,144 in, 65,536 out. Input text.
- Reasoning: yes, effort low, medium, high, xhigh, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: may-use-during-free-period. Data may train the model. Retained 30 days.
- Route: Atomic edits and bulk mechanical work only. Banned from agentic roles.

### opencode/big-pickle
- Name: Big Pickle. Status: active. Released: 2025-10-17. Knowledge cutoff: 2025-01.
- Description: Reasoning model for deliberate analysis, multi-step problem solving, and tool use
- Context: 200,000 in, 32,000 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Structured output: yes. Open weights: no.
- Policy: may improve the model during the free period. Retention not stated.
- Route: Archived. Text only, dominated by DeepSeek.
<!-- END AUTO:cards -->

Cards cover every free model that is not archived, plus Big Pickle, which keeps full specs in section 8. Iceman, Inkling, and MiniMax M3.1 keep their specs in section 8 and their policy in section 12, so restoration needs no research.

## 10. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-11-16

- OpenCode 2.2.7 at `/usr/bin/opencode`. Config `/home/amol/.config/opencode/opencode.json`.
- Credentials: three Console API keys in `auth.json`. The gateway key lives in the gateway env file. Values never recorded.
- Configured prefixes: three Console API key accounts, seven `omni-*` gateway providers, one OAuth `opencode/` stream, and one local `ornith/` route.
- `opencode.json` stores no key material except the dummy 5-character `ornith` key. That server is closed.
- Default model: `opencode/deepseek-v4.1-flash-free`. The file was not edited on 2026-11-16. It still carries orchestrator agent `orchestrator` on `opencode-college/longcat-2.5-free` and subagents on `opencode-college/deepseek-v4.1-flash-free`.
- The routing change to Hy4 flash is ledger policy only. It lives in section 3.1, not in the config. Edit the config only after a measured step count beats DeepSeek.
- Small model: `omni-codex/codex/gpt-5.6-luna`. Title agent: `omni-groq/groq/qwen/qwen3.8-27b`. Compaction agent: `omni-codex/codex/gpt-5.6-luna`.
- Gateway: OmniRoute 3.8.52, `http://localhost:20128/v1`, healthy, 14 requests in 24 h, uptime 8 s at probe.
- Gateway connections: 14 configured, 13 active, 1 unavailable.
- Local servers on 11434, 1234, 8000, 8080: none listening.
- Steps this period: 1.4k over 29 prompts, 10 sessions, 24 subagents, 7 active days.

## 11. Drift watchlist

- Console free list grew from 12 to 13 models between 2026-11-09 and 2026-11-16. New: `hy4-flash-free`, `minimax-m3.1-flash-free`. Retired: `crouton-alpha-free`.
- Hy4 flash is closed-weight with no independent benchmark sweep. Tencent's numbers come from its own launch post. Treat them as directional.
- Tencent positions Hy4 flash for everyday work and the full Hy4 for long-horizon engineering. Do not attribute full-Hy4 scores to the flash ref.
- `opencode models` lists `hy4-flash-free` once, under `opencode/`. The Console catalog carries it under all four prefixes, and all four answered on 2026-11-16. The CLI under-reports this ref. Trust the catalog plus a probe.
- Crouton left the catalog and the CLI. models.dev marks it deprecated. Any archived copy of the ledger that still routes to it is stale.
- The Console catalog reports LongCat as 0 context and text only. models.dev and the official docs report 1M context and text plus image. Trust models.dev and the docs for capability. Trust the Console catalog for price.
- LongCat carries a 100 request/day cap. It is shared across all four Console prefixes. Rotating accounts does not reset it.
- The LongCat reset boundary is not published. Until a probe answers, treat the day as spent. The cap reset between 2026-11-09 and 2026-11-16.
- The Hy4 flash cap is vendor-stated as near-unlimited with no number, and the window closes 2026-11-30. Tencent's own free WorkBuddy access and half-price TokenHub pricing close the same day.
- Gateway model entries hold at 444. Catalog holds at 378. Wired refs rose from 96 to 112.
- Codex expiry holds at 2026-11-19. `cline` moved from 2026-11-09 to 2026-11-16 and expires today.
- `nemotron-3-ultra-free` is still listed in the docs free set but absent from the Console catalog.
- `minimax-m3.1-flash-free` is `active` under `opencode/` but `deprecated` under `opencode-go/` in models.dev. Verify before routing outside the Console.
- Console free list and gateway connection list change independently. Check both.
- `duckduckgo-web` is active but exposes no wired models. Recheck after each gateway upgrade.
- `gemini` model map is stale. Verify a branded model before routing.
- Catalog and CLI disagree. Catalog gateway 378, `/v1/models` 444, CLI 112 gateway refs.
- Console catalog counts 220 entries across three accounts, paid models included. The CLI exposes 125 Console refs: 89 OAuth and 36 API key.
- Use the CLI as the routing source of truth for Console refs, except for Hy4 flash. Use `/v1/models` for gateway refs.

## 12. Privacy and data handling

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| Hy4 flash Free | not used | zero retention for the free window |
| LongCat 2.5 Free | not used | zero retention; hosted in China |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| GLM-5.3-Flash Free | not used | zero retention |
| Beam Free | not used | 30 days, regional blocklist |
| MiniMax M3.1 Flash Free | not stated | not stated |
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
- R12 durable default: the root default must be a permanent model. A dated window may hold a task class, never the root setting. A config that outlives its model is a broken config.
- R13 capability escalation: start on the cheapest route that clears the class gate. Escalate only for a capability, modality, or context gap, never for price. Record the trigger in section 3.1.
- Override: a dated measurement with a stated method replaces a pick.

## 14. Provenance and measurements

Checked 2026-11-16.

- Host: `opencode --version` 2.2.7, `opencode stats` 29 prompts and 1.4k steps, `opencode auth list` 3 Console accounts, `opencode models` 230 lines.
- Gateway: OmniRoute 3.8.52, `providers status` 14 connections, `/v1/models` 444 entries, catalog 378 entries, CLI 112 gateway refs. Health: 14 requests in 24 h, uptime 8 s. The service had just restarted.
- Catalog file: 220 Console entries across three accounts, 378 gateway entries. CLI: 230 refs. Console API-key prefixes expose 13 free refs each.
- Console probes 2026-11-16: 13 of 13 free models answered. Times: big-pickle 4 s, beam 2 s, deepseek 5 s, flint 2 s, glm 2 s, hy4 3 s, iceman 3 s, inkling 2 s, longcat 6 s, mimo 2 s, minimax 2 s, lightning 2 s, super-vl 2 s.
- Crouton: `opencode-college/crouton-alpha-free` returned `Model unavailable`. models.dev marks it deprecated. It left the free set.
- Hy4 prefix residency 2026-11-16: answered on all four prefixes. `opencode-ronaldo/` needed one retry on `The inference service is overloaded`.
- LongCat: answered on college and personal at the 2026-11-16 probe. The 100/day cap reset since 2026-11-09.
- Gateway probes 2026-11-09: `codex/gpt-5.6-luna` 10 s, `codex/gpt-5.6-terra` 3 s. Both answered OK. Not re-probed on 2026-11-16.
- Prior gateway probes 2026-10-30: `openrouter/nvidia/nemotron-3.5-super-vl:free` 10 s, `mistral/codestral-latest` 7 s, `openrouter/poolside/laguna-xs-2.1:free` 2 s.
- Gateway failure: `openrouter/google/gemma-4-31b-it:free` returned no reply in 75 s on 2026-10-30. Removed as a fallback.
- Direct credential probe to the Console endpoint returns 403. Quota is not readable outside OpenCode.
- Benchmark sources: Hy4 flash launch table checked 2026-11-16. Terminal-Bench 4.0 19.2, ProgramBench 16.0, AutomationBench 52.2, 224B total and 12B active parameters. Blind test over 259 tasks with 85 internal users: Hy4 flash 2.79 of 4 at 6.7 min and 0.26 yuan, DeepSeek V4.1 Flash 3.09 at 10.3 min and 1.20 yuan, GLM-5.3-Flash 2.53 at 7.6 min and 0.81 yuan.
- Benchmark sources: LongCat vendor table checked 2026-11-09. DeepSeek DeepSWE 77.0, Terminal-Bench 4.0 42.3, ProgramBench 29.1, AutomationBench 60.5 from the Tencent table. SkillsBench 69.8 from Vals.
- BenchLM keeps DeepSeek V4.1 Flash at 64.43 overall against Hy4 preview at 60.32. That comparison is the preview, not the flash tier, and it is not a host measurement.
- Vendor framing: Tencent recommends Hy4 flash for deep search, documents, slides, simple frontend demos, and computer use, and the full Hy4 for long-horizon software engineering.
- Prior probe: 11 of 12 Console free refs answered on 2026-11-09. Eight answered on 2026-10-30.
- Every launch table above is directional. No free Console model has a host-measured step count yet.

Measurement protocol for a pick change:

1. Fix one task with a stated acceptance check.
2. Run the candidate and the incumbent on the same task.
3. Record prompt, steps, tool calls, domain, and pass or fail.
4. Promote on fewer steps with a pass. Demote on failure, not on slowness.

## 15. Stats

<!-- BEGIN AUTO:stats -->
- Console refs: 125 across 4 prefixes. 89 OAuth, 36 API key.
- Console free models: 13 distinct.
- Console free refs: 52, from 13 models across 4 prefixes. The CLI lists hy4 flash once, so it reports 49.
- Routed Console accounts: 3. Anonymous `opencode/` is OAuth fallback.
- Gateway connections: 14 configured. Working 5, partial 3, no models 5, unavailable 1.
- Gateway providers usable: 7. Fully working 4, partial 3.
- Gateway model entries: 444 live, 378 catalog, 112 wired.
- Shortlist: 8 entries across 2 domains.
- Live windows: Hy4 flash to 2026-11-30, LongCat 2.5 to 2026-11-23. Neither is a default.
- Key material in `opencode.json`: none, except the dummy local `ornith` key.
- Next credential expiry: cline 2026-11-16, codex 2026-11-19.
- Host: OpenCode 2.2.7, OmniRoute 3.8.52, Arch Linux ARM64 chroot, `arch-btw`.
- Collected: 2026-11-16.
<!-- END AUTO:stats -->
