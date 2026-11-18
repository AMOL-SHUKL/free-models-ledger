# Free Models Ledger

Routing table for zero-price model access on this device. Two quota domains exist: OpenCode Console, capped per account, and the OmniRoute gateway, capped per upstream. The unit of cost is one request, not one token.

**State 2026-11-18. OpenCode 2.3.2, gateway 3.8.53. 3 routed Console accounts, 11 Console free models. DeepSeek V4.1 Flash Free is the root default, the orchestrator, and the top free model. Inkling 2 Small Free is second and owns documents, images, and 1M context. GLM-5.3-Flash Free was retired on 2026-11-18 and is gone. Data retention is recorded, never ranked. No key material in `opencode.json`.**

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

Data retention is not a metric. A model is never demoted for training use, retention length, or hosting country. Policy is recorded in section 12 so a human can decide, not so the router can filter.

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
| Gateway Cline | `omni-cline/` | OAuth | plan quota, expired | partial | 3 |
| Gateway Gemini | `omni-gemini/` | API key | free tier, gemma only | partial | 3 |
| Gateway Groq | `omni-groq/` | API key | free tier, rejects agent prompt | partial | 3 |
| Local | `ornith/` | dummy key | none, server closed | offline | - |

LongCat 2.5 Free is not a separate domain. It shares the Console auth. Its cap is 100 requests per day and that cap belongs to LongCat alone. A `Free usage limit reached` answer on DeepSeek or Inkling is an upstream throttle that resets on its own schedule, not the LongCat counter. Rotating accounts does not reset LongCat.

Hy4 flash Free is not a separate domain either. It shares the Console auth and resolves on all four prefixes. Its cap is vendor-stated as near-unlimited, with no published number, and the window closes 2026-11-30. Near-unlimited is not unlimited: treat it as a promotional route, never as a permanent default.

The three Console accounts provide the routed OpenCode surface. The API key streams (`opencode-college/`, `opencode-personal/`, `opencode-ronaldo/`) serve automated agent execution first. The OAuth stream (`opencode/`) serves user interactive sessions second. The gateway (`omni-*`) is last. DeepSeek orchestrates from the Console API-key stream. Inkling takes documents and large context. Subagents stay on Console free models.

Verified 2026-10-28: OAuth user seats and Console API keys drain separate quota buckets in the same workspace. Console College logged 536 steps in one day at zero cost (171 via OAuth user, 365 via API key). The LongCat cap is the exception: it applies to one model, not to the Console stream.

The gateway is the last resort. Spend a gateway step only when both Console streams are throttled or the class needs a gateway-only model. Exception: hard agentic work escalates to Codex terra. No `apiKey` is stored in `opencode.json`. The gateway does not enforce auth on chat completions, only on the models endpoint.

## 3. Shortlist

Eight entries. Each wins one task class in one domain. Anything absent is archive.

| Class | Ref | Domain | Why it wins | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| Root default, hard agentic | `opencode-college/deepseek-v4.1-flash-free` | Console API | Highest free AA Index, 32.6. DeepSWE 77.0, Terminal Bench Science 12.9, Vibe Code 87.7, ComplexConstraints 50.4, all best in class | personal, ronaldo, then `omni-codex/codex/gpt-5.6-terra` |
| Documents, images, 1M context | `opencode-college/inkling-2-small-free` | Console API | Best free document model. GDP.pdf 25.2, OmniDocBench 94.6, PerceptionBench 64.7, ZeroBench 44.0, AA-LCR 83.3. 1M context | `opencode-college/nemotron-3.5-super-vl-free` |
| Video and audio input | `opencode-college/mimo-v2.6-flash-free` | Console API | Only free route with audio input, and the only one with video after the GLM and MiniMax retirements | none |
| Orchestration, long-horizon | `opencode-college/longcat-2.5-free` | Console API | AutomationBench 65.1 on its vendor table. 262k output, the largest free reply window | `opencode-college/deepseek-v4.1-flash-free` |
| Routine, bulk, cheapest step | `opencode-college/hy4-flash-free` | Console API | 0.26 yuan per task against DeepSeek 1.20. Median 6.7 min against 10.3. Free to 2026-11-30 | `opencode-college/deepseek-v4.1-flash-free` |
| Vision, large context | `opencode-college/nemotron-3.5-super-vl-free` | Console API | 1M input with images, effort to xhigh, open weights | `omni-openrouter/openrouter/nvidia/nemotron-3.5-super-vl:free` |
| Cheap background | `opencode-college/nemotron-3.5-lightning-free` | Console API | 1 to 2 s, 262k output, no tiers | `omni-openrouter/openrouter/poolside/laguna-xs-2.1:free` |
| PDF input | `omni-codex/codex/gpt-5.6-luna` | gateway | Only route with pdf input | `omni-codex/codex/gpt-5.6-terra` |

DeepSeek holds the default because it is permanent, measured, and leads every hard coding and instruction-following row. It is not promoted on a date and it does not depend on a window.

Inkling 2 Small takes second place and the whole document class. It does not trail GLM-5.3-Flash; on Thinking Machines' own head-to-head it beats GLM on every document, vision, and long-context row. It is slower than DeepSeek on terminal work, 5.7 against 12.9 on Terminal Bench Science, so it never takes the default.

The document class needed a replacement because GLM-5.3-Flash held the only free text, image, and video lane. GLM retired on 2026-11-18. The lane splits: Inkling takes documents, scans, charts, and still images at 1M context, and MiMo takes video and audio.

### 3.1 Escalation ladder

The orchestrator starts every task on DeepSeek and moves on evidence, not on feel. The ladder is ordered by what each model provably wins.

1. Stay on `deepseek-v4.1-flash-free` for repo-scale edits, terminal work, test loops, instruction-heavy tasks, and orchestration. It leads DeepSWE, Terminal Bench Science, Vibe Code, and ComplexConstraints among free models.
2. Move to `inkling-2-small-free` for documents, scans, screenshots, charts, and any input above 200k tokens. It leads every document row and carries 1M context.
3. Move to `longcat-2.5-free` when the task is long-horizon automation or needs more than 32k output in one reply. Its 100/day cap makes it an escalation route, not a default.
4. Move to `hy4-flash-free` for cheap one-pass bulk work while its window is open. It trails DeepSeek on every hard coding benchmark, so never send it a task that must pass a test loop.
5. Move to `mimo-v2.6-flash-free` for audio or video input. No other free route carries either.
6. Never escalate for cost. Every Console free route costs zero on request count. Escalate for capability, modality, or context only.
7. Never escalate or demote on data policy. Retention and training use do not enter the ladder.

Cross-vendor benchmark tables are not comparable. LongCat's AutomationBench 65.1 and Thinking Machines' DeepSeek rows come from different harnesses. Use them to rank within a table, never across two.

Selection order: fewest expected steps, then cheapest domain, then lowest variance. Data policy is not a tiebreak.

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
| `cline` ronaldoamol.9699 | OAuth | expired | 15 refs wired. `beam-free` returns 500. Credential expires 2026-11-18 |
| `gemini` main | API key | partial | 42 entries, 21 refs wired. Gemma models work, branded models 404 |
| `groq` main | API key | partial | 15 entries, 8 refs wired. OpenCode gets 413, prompt too large |
| `duckduckgo-web` | none | active | No wired models. `omni-ddg` absent from CLI |
| `cloudflare-playground` | none | active | No wired models. Needs Playwright and Chromium |
| `theoldllm` | none | active | No wired models |
| `chipotle` | none | active | No wired models |
| `uncloseai` | none | active | No wired models |
| `felo-web` | none | unavailable | Connection disabled |

### 4.2 Catalog-only providers

None observed. `/v1/models` exposes 480 entries across the same seven providers. The catalog exposes 385 entries. No provider appears in the catalog without a connection.

### 4.3 Gateway notes

- `duckduckgo-web` is active but exposes no wired models. The `omni-ddg` prefix is absent from `opencode models`. Do not route there.
- `gpt-6.1-terra` is not listed on the gateway now. Codex carries `gpt-5.6-terra`, `gpt-5.6-luna`, and `gpt-6-luna`.
- `cline` returns 500 on some models and 402 on paid ones. Its credential expired 2026-11-16. Treat it as dead until re-auth.
- `aihorde` works anonymously through the `aihorde/` prefix. The `horde/` alias requires a key. An account key would raise queue priority, not add models.
- `groq` works at the gateway and fails inside OpenCode. The agent system prompt exceeds the model limit. Do not route agent sessions there.
- `gemini` serves gemma models only. `gemma-4-31b-it` is confirmed working.
- The gateway does not enforce auth on chat completions. It does on `/v1/models`. No `apiKey` is stored in `opencode.json`, and provider routes still work.
- Codex account priority is 1 for ronaldoamol and 2 for amolshukla. The second drains only after the first throttles.
- `providers test-all` on 2026-11-09: 4 API-key connections valid, 8 OAuth or no-auth connections skipped, 2 inactive or unavailable.
- The gateway reports 480 model entries. The catalog reports 385. OpenCode wires 111. Entry count is not availability.
- No free model is served by the gateway. Every free route is on Console or OAuth.

## 5. Step budget

Console steps are the scarce resource. Spend Console API keys first, then OAuth, then the gateway.

| Share | Steps | Class | Ref |
| :--- | ---: | :--- | :--- |
| 35% | 70 | hard agentic, orchestration, review | deepseek |
| 20% | 40 | documents, images, large context | inkling |
| 15% | 30 | routine agentic and bulk edits | hy4 flash, then deepseek |
| 10% | 20 | long-horizon automation | longcat |
| 10% | 20 | background scans | lightning |
| 5% | 10 | video and audio input | mimo |
| 5% | 10 | second vision route | super vl |

Rules:

- Try the Console API key first. Move to OAuth when the API keys throttle. Use the gateway when both Console streams fail or the class needs a gateway-only model.
- Start every task that must pass on DeepSeek. It carries the largest share.
- Send documents and large context to Inkling before spending a DeepSeek step on them.
- On 2026-11-30 the Hy4 flash share returns to DeepSeek. No rule depends on the promotion staying live.
- Rotate Console prefixes on throttle, not on model quality. LongCat is the exception. Its 100/day cap is shared across prefixes, so a LongCat limit hit is final for the day.
- Give LongCat the long-horizon share only. Subagents run DeepSeek or Inkling.
- Keep 20% of the Console budget for the last quarter of the day.
- Use one orchestrator per task. Nested subagents multiply steps in one domain.

## 6. Promotions, uncapped domains and expiry

A domain outside the 200/day cap changes the ranking for the classes it covers. Today that is the gateway: Codex plan quota, OpenRouter free tier, Mistral free tier.

A promotion is not an uncapped domain. It has a start, an end, a cap, and a fallback. Two windows are live.

| Window | Ref | Start | End | Cap | State |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Hy4 flash free | `opencode-college/hy4-flash-free` and all four Console prefixes | 2026-11-16 | 2026-11-30 | near-unlimited, no published number | live; answered on all four prefixes at the 2026-11-16 probe |
| LongCat 2.5 free | `opencode-college/longcat-2.5-free` and all four Console prefixes | 2026-11-02 | 2026-11-23 (review) | 100 requests/day, shared across all four prefixes | live; answered 2026-11-18 |

Closed windows: GLM-5.3-Flash Free retired 2026-11-18. MiniMax M3.1 Flash Free retired 2026-11-17. Crouton Alpha Free retired 2026-11-09. None returns.

Rules:

- Hy4 flash takes cheap bulk work. It does not take the root default. A default that expires is a stale config.
- Use LongCat for long-horizon automation and replies above 32k output. Do not spend it on bulk edits.
- On `Free usage limit reached`, switch to Inkling, then LongCat, and keep working. A DeepSeek or Inkling throttle resets on the provider schedule.
- The Hy4 flash cap is described as near-unlimited, not numeric. Never plan a batch on the assumption that it cannot throttle.
- The LongCat reset boundary is not published. Recheck with a one-line probe at the start of each session; until it answers, treat the day as spent.
- The 2026-11-23 LongCat end date is a workstation review date, not a vendor date. The vendor states only that the model is free for a limited time.
- On 2026-11-30, remove Hy4 flash from the shortlist. DeepSeek absorbs the bulk share.
- On 2026-11-23, remove LongCat from the shortlist. DeepSeek is the permanent default.
- A promotion never blocks a session. The root default stays DeepSeek.

Expiry is not a promotion. It is the date the stored credential stops working. `omniroute providers status` reports it per connection.

| Connection | Expires | Effect on expiry |
| :--- | :--- | :--- |
| `codex` ronaldoamol.9699 | 2026-11-19 | Route fails until re-auth |
| `codex` amolshukla.bcr21221 | 2026-11-19 | Second account, same date |
| `cline` ronaldoamol.9699 | 2026-11-18 | Expires today. Route fails until re-auth |

Rules:

- In an uncapped domain rank by capability, not steps.
- Keep specialists for pdf, audio, and 1M vision. Codex covers pdf only.
- Record an expiry date for every connection that has one. A lapsed credential looks like a 500 or a timeout.
- On expiry, fall through to section 3. Never leave a dead route as default.
- Re-auth is a user action. The ledger only records the date.
- Keep the small model, title, and compaction off any expiring gateway credential.

## 7. Archive, do not route

| Ref or connection | Why excluded |
| :--- | :--- |
| `opencode/glm-5.3-flash-free` | Retired 2026-11-18. `Model unavailable` on all four prefixes. Still listed under the three API prefixes as a phantom. Video lane passed to MiMo |
| `opencode/minimax-m3.1-flash-free` | Retired 2026-11-17. Absent from the CLI and the catalog |
| `opencode/crouton-alpha-free` | Retired 2026-11-09. models.dev marks it deprecated |
| `opencode/big-pickle` | Free, but text only, closed weights, no unique modality. Dominated by DeepSeek |
| `opencode/nemotron-3-ultra-free` | Absent from the Console free catalog on 2026-11-09 |
| `opencode/longcat-2.5-preview-free` | Retired preview. Replaced by `longcat-2.5-free` on 2026-11-02 |
| `opencode/*` paid refs | 79 non-free refs. No place in a free ledger |
| `omni-groq/*` | Prompt size rejected inside OpenCode. Gateway-only use |
| `omni-gemini/gemini-*` | Branded models 404. Gemma models remain usable |
| `omni-cline/cline/reflection/beam-free` | HTTP 500 at the provider |
| `omni-cline/*` paid refs | 402 Insufficient credits |
| `duckduckgo-web` | Active connection, no wired models |
| `cloudflare-playground`, `theoldllm`, `chipotle`, `uncloseai` | Active connections, no wired models |
| `felo-web` | Unavailable |
| `ornith/ornith-1.5-9b` | Configured local route, server on 8080 is closed |
| `aihorde/*` | Works anonymously, but the shared queue is slower than Console for background work |

The Console list turned over sharply since 2026-11-16. Three free models retired in nine days: Crouton, MiniMax M3.1, GLM-5.3-Flash. Eleven remain.

A model leaves archive only with a measured step count that beats a listed pick. Record it in section 14.

## 8. Console inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Knowledge | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | 2026-06-30 | reflection/beam-1.0-501b-a23b |
| opencode/big-pickle | 200,000 | 32,000 | text | yes | - | 2025-10-17 | 2025-01 | none |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | low, high, max | 2026-10-17 | 2025-05 | deepseek/deepseek-v4.1-flash-1017 |
| opencode/flint-40b-free | 262,144 | 65,536 | text | yes | low, medium, high, xhigh, max | 2026-10-21 | 2026-06 | recursive/flint |
| opencode/hy4-flash-free | 1,048,576 | 65,536 | text, image | yes | none, high | 2026-11-16 | - | tencent/hy4-flash |
| opencode/iceman-free | 262,144 | 131,072 | text | yes | - | 2026-11-06 | - | none |
| opencode/inkling-2-small-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, max | 2026-11-05 | - | thinkingmachines/inkling-2-small |
| opencode/longcat-2.5-free | 1,000,000 | 262,144 | text, image | yes | toggle | 2026-11-02 | - | meituan/longcat-2.5 |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | - | xiaomi/mimo-v2.6-flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | 2025-09 | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | - | nvidia/nemotron-3.5-super-vl-120b-a12b |
<!-- END AUTO:inventory -->

Each free model is also reachable under `opencode-college/`, `opencode-personal/`, and `opencode-ronaldo/`. Those 33 duplicate refs add no distinct model. They add quota buckets. The LongCat and Hy4 flash caps are shared across them.

`opencode/` carries 10 free refs. It does not list Big Pickle, and it does not list GLM.

GLM is a phantom ref. All three API prefixes still list `glm-5.3-flash-free`, and it fails every call with `Model unavailable`. Do not count it. Probe before routing any retired-looking ref.

`opencode models` lists Hy4 flash once, under `opencode/`. The Console catalog carries it under all four prefixes and every prefix answered a live probe on 2026-11-16. Trust the catalog and the probe over the CLI count for this ref.

## 9. Cards

<!-- BEGIN AUTO:cards -->
### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-10-17. Knowledge cutoff: 2025-05.
- Description: October 17 update of DeepSeek V4.1 Flash with improved reasoning, coding, agentic tasks, and visual understanding
- Context: 200,000 in, 32,000 out. Input text, image. 552B total, 16B active.
- Reasoning: yes, effort low, high, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: zero-retention. Prompts and responses are not used for training. Recorded, not ranked.
- Route: Root default, orchestrator, and the hard agentic escalation step. Keep context tight.
- Benchmark: AA Index 32.6, the highest free score. DeepSWE 77.0, Terminal Bench Science 12.9, Vibe Code 87.7, ComplexConstraints 50.4, SWEBench Pro 89.7, AA-Briefcase 1570, GDPVal 1746, Tax Agent 44.4. Highest task satisfaction in Tencent's 259-task blind test, 3.09 of 4.
- Weakness: non-hallucination rate 35.9, the lowest of the contenders. Verify factual claims on document work and move that work to Inkling.

### opencode/inkling-2-small-free
- Name: Inkling 2 Small Free. Status: active. Released: 2026-11-05.
- Description: Thinking Machines' open-weight multimodal MoE for long-horizon agentic work, documents, and vision.
- Context: 1,048,576 in, 131,072 out. Input text, image. 594B total, 17B active.
- Reasoning: yes, effort none, minimal, low, medium, high, max. Tools: yes. Structured output: not stated. Open weights: yes.
- Policy: retained during the free period and may be used to monitor performance and train the model. Region blocklist includes the EU and Norway. Recorded, not ranked.
- Route: Second overall and first for documents, scans, charts, screenshots, finance and legal work, and any input above 200k.
- Benchmark: AA Index 30.6, Vals Index 56.1. GDP.pdf 25.2, OmniDocBench 94.6, PerceptionBench 64.7, ZeroBench 29.0/44.0, MuirBench 79.2, Chartography 36.7/82.1, MMMU Pro 81.6. AA-LCR 83.3, LongBench v2 70.6, ARC-AGI-2 81.7, HLE-Diamond 28.7. JobBench 70.5, EnterpriseBench 71.8, Finance Index 61.5. Non-hallucination 73.3, the best free rate.
- Weakness: Terminal Bench Science 5.7 against DeepSeek 12.9, and ComplexConstraints 44.5 against 50.4. Do not use it for terminal loops or instruction-dense agent runs.

### opencode/longcat-2.5-free
- Name: LongCat 2.5 Free. Status: active. Promotional to 2026-11-23. Released: 2026-11-02.
- Description: Meituan's multimodal reasoning model for long-horizon coding and agent tasks, with visual understanding and a 1M-token context window
- Context: 1,000,000 in, 262,144 out. Input text, image.
- Reasoning: yes, toggle. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: zero-retention, not used for training. Hosted in China. Recorded, not ranked.
- Route: Long-horizon automation and any single reply above 32k output. 100 requests/day shared across all Console prefixes.
- Benchmark: Vendor table. Terminal Bench v2.1 88.4, v4.0 38.5, AutomationBench 65.1, DeepSWE 64.6, SkillsBench 63.3. Trails DeepSeek on DeepSWE by 12.4 and SkillsBench by 6.5. Cross-table comparison is directional only.
- Window: closes 2026-11-23 at the workstation review. Never the root default, and never the orchestrator.

### opencode/hy4-flash-free
- Name: Hy4 flash Free. Status: active. Promotional window to 2026-11-30. Released: 2026-11-16.
- Description: Tencent's lower-cost Hy4 tier for everyday agent and productivity tasks, with image understanding and a 1M-token context window.
- Context: 1,048,576 in, 65,536 out. Input text, image.
- Reasoning: yes, effort none, high. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: zero-retention during the free window. Recorded, not ranked.
- Route: Cheap one-pass bulk work to 2026-11-30. Near-unlimited usage, not unlimited. Falls back to DeepSeek on throttle or expiry.
- Benchmark: Tencent launch table. Terminal-Bench 4.0 19.2, ProgramBench 16.0, AutomationBench 52.2. Blind test over 259 tasks: satisfaction 2.79 of 4, median 6.7 min, 0.26 yuan per task, against DeepSeek 3.09 at 10.3 min and 1.20 yuan. Below DeepSeek on every hard coding benchmark.
- Escalation: hand repo-scale, terminal, and multi-pass work to DeepSeek. Tencent recommends the full Hy4, not this tier, for long-horizon engineering.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22.
- Description: MiMo Flash model for multimodal coding agents and long-context automation
- Context: 200,000 in, 32,000 out. Input text, image, audio, video. 309B total, 15B active.
- Reasoning: yes, no effort control. Tools: yes. Structured output: not stated. Open weights: yes.
- Policy: collected during the free period to improve the model. Recorded, not ranked.
- Route: Sole audio route and sole video route after the GLM and MiniMax retirements.
- Benchmark: AA Index 26.7, Vals Index 54.8, SWEBench Pro 89.7, Vibe Code 79.0. Document scores trail Inkling: GDP.pdf 9.0 against 25.2, OmniDocBench 87.4 against 94.6.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15.
- Description: NVIDIA vision-language reasoning model for text, image, and video understanding and agentic tool use
- Context: 1,048,576 in, 131,072 out. Input text, image.
- Reasoning: yes, effort none, minimal, low, medium, high, xhigh. Tools: yes. Structured output: not stated. Open weights: yes.
- Policy: NVIDIA trial terms. Logged for security and product improvement. Recorded, not ranked.
- Route: Second vision route and the fallback when Inkling throttles.

### opencode/nemotron-3.5-lightning-free
- Name: Nemotron 3.5 Lightning Free. Status: active. Released: 2026-08-11. Knowledge cutoff: 2025-09.
- Description: Fast NVIDIA Nemotron MoE for reliable agentic tasks across enterprise workloads
- Context: 262,144 in, 262,144 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: NVIDIA trial terms. Recorded, not ranked.
- Route: Background scans, classification, and summaries. Small model and title generation.

### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts reasoning model with 23B active parameters for coding and agentic workflows
- Context: 1,048,576 in, 131,072 out. Input text.
- Reasoning: yes, effort low, medium, high, xhigh, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: retained 30 days for safety. Regional blocklist applies. Recorded, not ranked.
- Route: Text-only 1M-context work and cross-family review. Off the shortlist while Inkling holds the 1M lane with vision.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts model with about 2.5B active parameters for local coding agents
- Context: 262,144 in, 65,536 out. Input text.
- Reasoning: yes, effort low, medium, high, xhigh, max. Tools: yes. Structured output: yes. Open weights: yes.
- Policy: may use data during the free period and retains it 30 days. Recorded, not ranked.
- Route: Atomic edits and bulk mechanical work only. Banned from agentic roles.

### opencode/iceman-free
- Name: Iceman Free. Status: active. Released: 2026-11-06.
- Description: Stealth model, free for a limited time.
- Context: 262,144 in, 131,072 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Structured output: not stated. Open weights: no.
- Policy: data retained and used to improve the model during the free period. Recorded, not ranked.
- Route: Unmeasured. Hold until a measured run beats a listed pick.

### opencode/big-pickle
- Name: Big Pickle. Status: active. Released: 2025-10-17. Knowledge cutoff: 2025-01.
- Description: Reasoning model for deliberate analysis, multi-step problem solving, and tool use
- Context: 200,000 in, 32,000 out. Input text.
- Reasoning: yes, no effort control. Tools: yes. Structured output: yes. Open weights: no.
- Policy: may improve the model during the free period. Retention not stated. Recorded, not ranked.
- Route: Archived. Text only, dominated by DeepSeek.
<!-- END AUTO:cards -->

Cards cover every free model that is not archived, plus Big Pickle. GLM, MiniMax, and Crouton keep their retirement reason in section 7.

## 10. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-11-18

- OpenCode 2.3.2. Config `/home/amol/.config/opencode/opencode.json`.
- Credentials: three Console API keys in `auth.json`. The gateway key lives in the gateway env file. Values never recorded.
- Configured prefixes: three Console API key accounts, seven `omni-*` gateway providers, one OAuth `opencode/` stream, and one local `ornith/` route.
- `opencode.json` stores no key material except the dummy 5-character `ornith` key. That server is closed.
- Default model: `opencode/deepseek-v4.1-flash-free`.
- Orchestrator agent: `opencode-college/deepseek-v4.1-flash-free`.
- Subagents `explore` and `general`: `opencode-personal/deepseek-v4.1-flash-free`.
- Documents subagent `documents`: `opencode-college/inkling-2-small-free`. Defined at `/home/amol/.config/opencode/agents/documents.md`. A config-only entry does not register; this build discovers new agents from Markdown files.
- Small model and title: `opencode-college/nemotron-3.5-lightning-free`. Compaction: `opencode-college/inkling-2-small-free`. No gateway credential holds a routing role.
- Gateway: OmniRoute 3.8.53, `http://localhost:20128/v1`, healthy.
- Gateway connections: 14 configured, 13 active, 1 unavailable.
- Local servers on 11434, 1234, 8000, 8080: none listening.
- Steps this period: 1.4k over 29 prompts, 10 sessions, 24 subagents, 7 active days.

## 11. Drift watchlist

- Console free list fell from 13 to 11 models between 2026-11-16 and 2026-11-18. Retired: `glm-5.3-flash-free` on 2026-11-18, `minimax-m3.1-flash-free` on 2026-11-17.
- GLM-5.3-Flash is now paid only, `opencode/glm-5.3-flash`. The free ref is absent from the `opencode/` prefix but still listed under the three API prefixes, where it returns `Model unavailable`. A listed ref is not a live ref.
- MiniMax is now paid only, `opencode/minimax-m3`. The free ref is absent from every prefix.
- The retirements arrived together and removed the only free video-capable models apart from MiMo. Any route that assumed GLM or MiniMax for video is stale.
- Inkling 2 Small carries an EU and Norway blocklist. It answered on this device on 2026-11-18. Re-probe after any network change.
- Inkling's weights are open under Apache 2.0. A local serving path is possible and unmeasured.
- Hy4 flash is closed-weight with no independent benchmark sweep. Tencent's numbers come from its own launch post. Treat them as directional.
- Tencent positions Hy4 flash for everyday work and the full Hy4 for long-horizon engineering. Do not attribute full-Hy4 scores to the flash ref.
- `opencode models` lists `hy4-flash-free` once, under `opencode/`. The Console catalog carries it under all four prefixes. The CLI under-reports this ref.
- The Console catalog reports LongCat as 0 context and text only. models.dev and the official docs report 1M context and text plus image. Trust models.dev and the docs for capability.
- LongCat carries a 100 request/day cap, shared across all four Console prefixes. Rotating accounts does not reset it.
- The LongCat reset boundary is not published. Until a probe answers, treat the day as spent.
- The Hy4 flash cap is vendor-stated as near-unlimited with no number, and the window closes 2026-11-30.
- Gateway model entries rose from 444 to 480. Catalog 385. Wired refs hold at 111.
- Codex expiry holds at 2026-11-19. `cline` shows 2026-11-18 and is already dead.
- `nemotron-3-ultra-free` is still listed in the docs free set but absent from the Console catalog.
- Console free list and gateway connection list change independently. Check both.
- `duckduckgo-web` is active but exposes no wired models. Recheck after each gateway upgrade.
- `gemini` model map is stale. Verify a branded model before routing.
- Catalog and CLI disagree. Catalog gateway 378, `/v1/models` 444, CLI 112 gateway refs.
- Use the CLI as the routing source of truth for Console refs, except for Hy4 flash. Use `/v1/models` for gateway refs.
- A Console retirement is silent at the ref level. The ref disappears from `opencode models` and the request returns `Model unavailable`. Probe the free set after every OpenCode upgrade.

## 12. Privacy and data handling

This section is a record. It does not rank, filter, or reorder any route.

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| LongCat 2.5 Free | not used | zero retention; hosted in China |
| Hy4 flash Free | not used | zero retention during the free window |
| Beam Free | not used | 30 days, regional blocklist |
| Nemotron 3.5 Lightning, 3.5 Super VL | NVIDIA trial terms | logged, not linked to identity |
| MiMo-V2.6-Flash Free | collected to improve the model | during the free period |
| Flint 40B Free | may improve the model | 30 days |
| Iceman Free | retained and used to improve the model | during the free period |
| Inkling 2 Small Free | may monitor performance and train | during the free period; EU and Norway blocked |
| Big Pickle | may improve the model | not stated |
| Codex via gateway | governed by the OpenAI account plan | check before use |
| OpenRouter, Mistral, Cline, Gemini, Groq | governed by each upstream account | check before use |

Rules:

- Record the policy. Do not route on it.
- Do not assume a model is safer because its vendor states zero retention. Hosting jurisdiction, logging, and upstream account terms still apply.
- Keep secrets out of prompts on every route. That is a workflow rule, not a model choice.
- Re-read the Console privacy page after any upgrade. The MiMo and GLM entries both changed inside one month.

## 13. Policy

- R1 step efficiency: fewest expected steps for the class wins.
- R2 quality gate: a model must clear the class minimum. A cheap failure is not a saving.
- R3 domain economy: Console API key first, then OAuth, then gateway. Failover across the three Console accounts before any model downgrade.
- R4 capability fit: tools, reasoning control, modalities, context length.
- R5 data policy is recorded, not ranked. Training use, retention length, and hosting country never demote a route.
- R6 review work uses a different model family than the author.
- R7 archive is inert. A model returns only with a measurement.
- R8 no key material in `opencode.json`. Keys live in `auth.json` or the gateway env file.
- R9 promotion overlay: a dated free window may take one class. It must name an end date, a cap, and a fallback. A promotion never blocks a session.
- R10 shared caps: rotate prefixes only on per-account caps. A model cap shared across prefixes does not fail over.
- R11 benchmark prior: a vendor table is directional. A competitor score is not a host measurement. Promote only on a step count from section 14.
- R12 durable default: the root default must be a permanent model. A dated window may hold a task class, never the root setting.
- R13 capability escalation: start on DeepSeek. Escalate only for a capability, modality, or context gap, never for price. Record the trigger in section 3.1.
- R14 modality routing: video and audio go to MiMo, documents and still images to Inkling, PDF attachments to Codex Luna.
- R15 expiry hygiene: no routing role may depend on a gateway credential with a known expiry date.
- Override: a dated measurement with a stated method replaces a pick.

## 14. Provenance and measurements

Checked 2026-11-18.

- Host: `opencode --version` 2.3.2 after an in-place upgrade from 2.2.7. Gateway 3.8.53 after a clean npm global install.
- Free set: 11 distinct live Console free models, from 13 on 2026-11-16. One phantom ref, `glm-5.3-flash-free`, is still listed under the three API prefixes.
- Console probes 2026-11-18, college prefix, wall clock: lightning 1 s, big-pickle 1 s, beam 2 s, flint 2 s, inkling 2 s, super-vl 2 s, deepseek 3 s, hy4 3 s, iceman 3 s, longcat 4 s, mimo 4 s. Eleven of eleven live models answered.
- GLM probe 2026-11-18, repeated after the upgrade: `opencode-college/`, `opencode-personal/`, `opencode-ronaldo/`, and `opencode/` returned `Model unavailable` for `glm-5.3-flash-free`. The API prefixes still list it.
- Retirement confirmation: the Console docs deprecated table lists GLM-5.3-Flash Free on 2026-11-18 and MiniMax M3.1 Flash Free on 2026-11-17.
- Catalog pull 2026-11-18 through `fm-sync`: gateway 480 advertised, 385 catalog rows, 105 usable chat models across 7 providers. Console: 3 accounts, 33 free chat refs, 11 free per account. Allowlist 17 patterns, unchanged.
- CLI counts 2026-11-18: 237 refs total, 111 `omni-*`, 43 free-priced refs across four prefixes.
- Gateway probes 2026-11-18: `codex/gpt-5.6-terra` 6 s, `codex/gpt-5.6-luna` 6 s. Both answered OK.
- Gateway connections 2026-11-18: 14 configured, 13 active, 1 unavailable. Expiry: both codex accounts 2026-11-19, cline 2026-11-18.
- Upgrade path 2026-11-18: `opencode upgrade` from 2.2.7 to 2.3.2. `npm i -g omniroute@3.8.53` from 3.8.52. A first install was killed mid-write and left an incomplete tree; a clean reinstall finished with 1327 packages and npm created its own binary link. No file was hand-placed.
- Benchmark sources: Thinking Machines launch table for Inkling 2 Small, checked 2026-11-18. It reports Inkling 2 Small, GLM-5.3-Flash, MiMo V2.6 Flash, MiniMax M3.1 Flash, and DeepSeek V4.1 Flash on one harness.
- Head-to-head from that table, free models only. AA Index: DeepSeek 32.6, Inkling 30.6, MiniMax 29.1, GLM 27.8, MiMo 26.7. Vals Index: DeepSeek 61.3, Inkling 56.1, MiMo 54.8, MiniMax 53.8, GLM 50.9.
- Coding rows: DeepSWE DeepSeek 77.0, Inkling 72.1, MiniMax 73.7, MiMo 67.9, GLM 63.4. Terminal Bench Science DeepSeek 12.9, Inkling 5.7, GLM 4.8, MiMo 4.3, MiniMax 3.3. Vibe Code DeepSeek 87.7, Inkling 81.6, MiMo 79.0, MiniMax 73.9, GLM 30.8.
- Instruction row: ComplexConstraints DeepSeek 50.4, Inkling 44.5, GLM 27.6, MiniMax 23.2, MiMo 22.1.
- Document rows: GDP.pdf Inkling 25.2, MiniMax 20.8, DeepSeek 17.2, GLM 15.4, MiMo 9.0. OmniDocBench Inkling 94.6, GLM 92.3, MiniMax 92.0, DeepSeek 91.9, MiMo 87.4. PerceptionBench Inkling 64.7, GLM 49.6, DeepSeek 47.7, MiniMax 47.1, MiMo 39.1. ZeroBench with Python Inkling 44.0, GLM 32.0, DeepSeek 23.0.
- Long-context rows: AA-LCR Inkling 83.3, DeepSeek 82.7, GLM 80.0, MiniMax 79.7, MiMo 74.3. LongBench v2 Inkling 70.6, DeepSeek 69.0, GLM 68.2, MiMo 60.7.
- Factuality row: non-hallucination rate Inkling 73.3, GLM 61.7, MiMo 47.8, DeepSeek 35.9, MiniMax 6.3.
- Knowledge-work rows: JobBench Inkling 70.5, DeepSeek 68.5, MiMo 61.2, GLM 60.7. EnterpriseBench-CoreCraft Inkling 71.8, DeepSeek 58.5, GLM 46.7. AA-Briefcase DeepSeek 1570, Inkling 1527, MiMo 1487, GLM 1451.
- Benchmark sources: Tencent blind test over 259 tasks and 85 internal users, checked 2026-11-16. DeepSeek satisfaction 3.09 at 10.3 min and 1.20 yuan. Hy4 flash 2.79 at 6.7 min and 0.26 yuan. GLM 2.53 at 7.6 min and 0.81 yuan.
- Benchmark sources: LongCat vendor table checked 2026-11-09. DeepSWE 64.6, AutomationBench 65.1, SkillsBench 63.3, Terminal Bench v2.1 88.4.
- Benchmark sources: Tencent launch table checked 2026-11-16. Hy4 flash Terminal-Bench 4.0 19.2, ProgramBench 16.0, AutomationBench 52.2.
- Every launch table above is directional. No free Console model has a host-measured step count yet.

Measurement protocol for a pick change:

1. Fix one task with a stated acceptance check.
2. Run the candidate and the incumbent on the same task.
3. Record prompt, steps, tool calls, domain, and pass or fail.
4. Promote on fewer steps with a pass. Demote on failure, not on slowness.

## 15. Stats

<!-- BEGIN AUTO:stats -->
- Console free models: 11 distinct live. 12 listed, including one phantom ref.
- Console free refs: 43 across 4 prefixes. 10 on `opencode/`, 11 on each API prefix.
- Retired in the last nine days: GLM-5.3-Flash Free, MiniMax M3.1 Flash Free, Crouton Alpha Free.
- Routed Console accounts: 3. Anonymous `opencode/` is OAuth fallback.
- Gateway connections: 14 configured. Working 5, partial 3, no models 5, unavailable 1.
- Gateway providers usable: 7. Fully working 4, partial 3.
- Gateway model entries: 480 live, 385 catalog, 111 wired.
- Shortlist: 8 entries across 2 domains.
- Live windows: Hy4 flash to 2026-11-30, LongCat 2.5 to 2026-11-23. Neither is a default.
- Key material in `opencode.json`: none, except the dummy local `ornith` key.
- Next credential expiry: codex 2026-11-19. No routing role depends on it.
- Host: OpenCode 2.3.2, OmniRoute 3.8.53, Arch Linux ARM64 chroot, `arch-btw`.
- Collected: 2026-11-18.
<!-- END AUTO:stats -->
