# Free Models Ledger

Declarative record of every zero-price model reachable from OpenCode on this device. Built for daily regeneration by any agent, on any device. Machine-owned sections sit between AUTO markers and are rewritten from `snapshot.json`. Everything else is stable narrative.

**Snapshot 2026-10-23. Run 2026-10-23-arch-btw. OpenCode 2.1.3. Catalog 458 models, 2 providers. Free 8.**

Sections: 1 Top 3 by category, 2 How to use, 3 Inventory, 4 Cards, 5 Devices, 6 Drift, 7 Privacy, 8 Ranking policy, 9 Provenance, 10 Stats.

## 1. Top 3 by category

Picks are heuristic. They derive from catalog metadata and the routing rules in section 8, not from benchmarks. A dated measurement is the only thing allowed to replace a pick. Valid variant suffixes come from the host catalog per model. An unknown variant is a resolution error.

### 1.1 Primary agentic coding and orchestration

| Pick | Ref | Why | Call |
| :--- | :--- | :--- | :--- |
| 1 | opencode/deepseek-v4.1-flash-free#max | Primary orchestrator. DeepSWE v1.1 77.0, Apex Bench 72.1. Run with tight context; delegates all execution to subagents | `opencode run --model opencode/deepseek-v4.1-flash-free#max "task"` |
| 2 | opencode/beam-free | 501B MoE, 23B active. 1M context, 131k output. Heavy agentic execution and multi-file implementation | `opencode run --model opencode/beam-free#high "task"` |
| 3 | opencode/glm-5.3-flash-free | Efficient coding agent. Image, video, and pdf input. Zero-retention provider | driver for mixed document and code work |


Small changes and atomic edits: `opencode/flint-40b-free` (SWE-bench Pro 89.7%, MoE 2.5B active). Best for surgical single-file atomic edits.

Fallback chain when a route throttles: beam-free, glm-5.3-flash-free.

### 1.2 Long-context analysis, 1M class

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/beam-free | 1M in, 131k out, text |
| 2 | opencode/nemotron-3.5-super-vl-free | 1M in, 131k out, image input |
| 3 | opencode/nemotron-3-ultra-free | 1M in, 128k out, text, reasoning |

Alternates: opencode/glm-5.3-flash-free (200k, document input), opencode/flint-40b-free (262k, coding focus).

### 1.3 Vision and multimodal

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/nemotron-3.5-super-vl-free | Dedicated vision-language reasoning model, 1M context, image input |
| 2 | opencode/glm-5.3-flash-free | Image, video, and pdf input |
| 3 | opencode/mimo-v2.6-flash-free | Image, audio, and video input |


### 1.4 Fast bulk execution and subagents

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/mimo-v2.6-flash-free | Flash class, 200k, multimodal input |
| 2 | opencode/flint-40b-free | Small changes and fast bulk. 262k in, 64k out, MoE with 2.5B active parameters |
| 3 | opencode/nemotron-3.5-lightning-free | 262k in and out, NVIDIA trial, fast bulk |

Alternates: opencode/glm-5.3-flash-free (200k, document input).

### 1.5 Deliberate reasoning and review

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/deepseek-v4.1-flash-free#max | Benchmark leader on DeepSWE (77.0) and Apex Bench (72.1). Full effort reasoning |
| 2 | opencode/beam-free#max | Full effort budget on the largest first-party route |
| 3 | opencode/nemotron-3-ultra-free | 1M context, open weights, reasoning |

Alternates: opencode/nemotron-3.5-super-vl-free (1M, reasoning effort none to xhigh).

### 1.6 Specialists and narrow utility

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/glm-5.3-flash-free | Only free route here with pdf input |
| 2 | opencode/mimo-v2.6-flash-free | Only free route here with audio input |

No router, classifier, or audio generation routes exist on this device today.

## 2. How to use free models in OpenCode

- Pick for the current session: `/models` in the TUI. The selection applies to the session and does not change config.
- One command run: `opencode run --model opencode/beam-free#high "Refactor parseToken"`.
- Default model: `"model": "opencode/glm-5.3-flash-free"` in `opencode.json`. The root model keeps provider and model only, not a variant.
- Agent model: `"agents": { "reviewer": { "model": "opencode/glm-5.3-flash-free" } }`. Expanded form accepts providerID, model, and variant. A subagent without its own model inherits the parent session model.
- Command model: `"commands": { "audit": { "template": "Audit $ARGUMENTS.", "model": "opencode/beam-free#high" } }`.
- Variants: append `#name`. Names come from the catalog, listed per model in sections 3 and 4. Unknown variants fail model resolution. Custom variants can be added with a `variants` array on a provider model entry; reasoning controls can be declared with `capabilities.reasoning`.
- Auth: first-party free routes use `OPENCODE_API_KEY` through OpenCode Console. `/connect` is the TUI path. Signed out, the paid `opencode` models stay hidden and the free set stays visible.
- Omniroute gateway: this device also runs a local provider on `http://localhost:20128/v1` with 449 models. Its prices are unmeasured, so treat it as unknown cost.
- Local zero-cost: OpenCode auto-discovers Ollama, LM Studio, and vLLM on default local addresses. None were listening on this device at collection time.
- Direct HTTP for apps: Console free chat route `https://opencode.ai/inference/openai/v1/chat/completions`. Model list: `https://opencode.ai/inference/v1/models`.
- Go endpoints exist but are not configured on this device. Go clients should send a stable session id in `x-opencode-session`. OpenCode does this natively.

## 3. Inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Policy | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | retained-30-days | reflection/beam-1.0-501b-a23b |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | low, high, max | 2026-09-10 | zero-retention | deepseek/deepseek-v4.1-flash |
| opencode/flint-40b-free | 262,144 | 65,536 | text | yes | low, medium, high, xhigh, max | 2026-10-21 | may-use-during-free-period | recursive/flint |
| opencode/glm-5.3-flash-free | 200,000 | 32,000 | text, image, video, pdf | yes | low, high, max | 2026-08-26 | zero-retention | zhipuai/glm-5.3-flash |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | may-use-during-free-period | xiaomi/mimo-v2.6-flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | nvidia-trial-terms | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | nvidia-trial-terms | nvidia/nemotron-3.5-super-vl-120b-a12b |
| opencode/nemotron-3-ultra-free | 1,000,000 | 128,000 | text | yes | - | 2026-06-04 | nvidia-trial-terms | nvidia/nemotron-3-ultra-550b-a55b |
<!-- END AUTO:inventory -->

## 4. Model cards

<!-- BEGIN AUTO:cards -->
### opencode/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Last updated: 2026-10-16. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts reasoning model with 23B active parameters for coding and agentic workflows
- Context: 1,048,576 in, 131,072 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, medium, high, xhigh, max. Host variants: low, medium, high, xhigh, max.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: beam. Canonical: reflection/beam-1.0-501b-a23b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: retained-30-days. Not used for training. Data is retained for 30 days for safety reasons.
- Region restriction: yes. Angola, Belarus, Cambodia, China including Hong Kong and Macau, Cuba, Democratic Republic of the Congo, Equatorial Guinea, Iran, Iraq, Laos, Libya, Nigeria, North Korea, Russia, Syria, Turkmenistan, Uzbekistan, Venezuela, Crimea, Donetsk and Luhansk regions of Ukraine.

### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-09-10. Last updated: 2026-10-17. Knowledge cutoff: 2025-05.
- Description: DeepSeek V4.1 Flash model for reasoning and agentic coding (1017 update: DeepSWE 77.0, Apex Bench 72.1). Primary high-level orchestrator
- Context: 200,000 in, 32,000 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: none toggle; effort low, high, max. Host variants: low, high, max.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: deepseek-flash. Canonical: deepseek/deepseek-v4.1-flash. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: zero-retention. Prompts and responses are not used for training. Console states a zero-retention policy for these routes.
- Use: primary high-level orchestrator. Top benchmark performer across DeepSWE and Apex Bench. Run with limited context; delegate execution to subagents.

### opencode/flint-40b-free
- Name: Flint 40B Free. Status: active. Released: 2026-10-21. Last updated: 2026-10-21. Knowledge cutoff: 2026-06.
- Description: Open-weight mixture-of-experts model with about 2.5B active parameters for local coding agents
- Context: 262,144 in, 65,536 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, medium, high, xhigh, max. Host variants: low, medium, high, xhigh, max.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: flint. Canonical: recursive/flint. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: may-use-during-free-period. During the free period, collected data may be used to improve the model. Data is retained for 30 days.
- Use: small changes and atomic edits. Excels at surgical edits instead of full unified diffs. Not part of the fallback chain.

### opencode/glm-5.3-flash-free
- Name: GLM-5.3-Flash Free. Status: active. Released: 2026-08-26. Last updated: 2026-08-26. Knowledge cutoff: -.
- Description: Native multimodal GLM model for efficient coding and long-horizon agent tasks
- Context: 200,000 in, 32,000 out.
- Modalities: input text, image, video, pdf. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, high, max. Host variants: low, high, max.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: glm-flash. Canonical: zhipuai/glm-5.3-flash. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: zero-retention. Prompts and responses are not used for training. Console states a zero-retention policy for these routes.

### opencode/mimo-v2.6-flash-free
- Name: MiMo-V2.6-Flash Free. Status: active. Released: 2026-09-22. Last updated: 2026-09-22. Knowledge cutoff: -.
- Description: MiMo Flash model for multimodal coding agents and long-context automation
- Context: 200,000 in, 32,000 out.
- Modalities: input text, image, audio, video. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: mimo. Canonical: xiaomi/mimo-v2.6-flash. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: may-use-during-free-period. During the free period, collected data may be used to improve the model.

### opencode/nemotron-3.5-lightning-free
- Name: Nemotron 3.5 Lightning Free. Status: active. Released: 2026-08-11. Last updated: 2026-08-11. Knowledge cutoff: 2025-09.
- Description: Fast NVIDIA Nemotron MoE for reliable agentic tasks across enterprise workloads
- Context: 262,144 in, 262,144 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron. Canonical: nvidia/nemotron-3.5-lightning. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15. Last updated: 2026-10-15. Knowledge cutoff: -.
- Description: NVIDIA vision-language reasoning model for text, image, and video understanding and agentic tool use
- Context: 1,048,576 in, 131,072 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, minimal, low, medium, high, xhigh. Host variants: none, minimal, low, medium, high, xhigh.
- Structured output: -. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron. Canonical: nvidia/nemotron-3.5-super-vl-120b-a12b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.

### opencode/nemotron-3-ultra-free
- Name: Nemotron 3 Ultra Free. Status: active. Released: 2026-06-04. Last updated: 2026-06-04. Knowledge cutoff: 2025-09.
- Description: Largest Nemotron 3 model for maximum open-weight reasoning and agent accuracy
- Context: 1,000,000 in, 128,000 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron. Canonical: nvidia/nemotron-3-ultra-550b-a55b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.
<!-- END AUTO:cards -->

## 5. Devices

### arch-btw, Arch Linux ARM64 chroot, recorded 2026-10-23

- OpenCode 2.1.3, installed at `/usr/bin/opencode`. Config source: `/root/.config/opencode` with `opencode.json`.
- Credentials: none authenticated. The omniroute provider keeps a key inline in `opencode.json`; no values are recorded here.
- Default model: `opencode/glm-5.3-flash-free`. The hidden `title` agent points at an omniroute model.
- Agents: build and plan primary, explore and general subagents, hidden system agents present.
- Local model servers on 11434, 1234, 8000: all closed.
- Reachability: all 8 free models answered a one-turn probe on 2026-10-23.

## 6. Drift watchlist

Absent free offers documented upstream: exo-free, mimo-v2.5-free, longcat-2.5-preview-free, ll-alpha-free, muse-spark-1.3-contributor-free, jev-1.13-free. Jev is a scoring endpoint, not a chat model.

Providers not configured on this device: opencode-go, groq, mistral, openrouter.

The omniroute gateway lists 449 models with empty cost arrays. Its pricing is unmeasured. No entry qualifies as free.

Watch rules: check the Console free models list and the Console models table daily. Treat a model as removed only after two runs absent from the host catalog. Use the catalog as the source of truth.

## 7. Privacy and data handling

Console states: all models hosted in the US and EU, zero-retention default, no training on data. Exceptions, as stated today:

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| DeepSeek V4.1 Flash Free | not used | zero retention |
| GLM-5.3-Flash Free | not used | zero retention |
| Beam Free, Console route | not used | 30 days for safety, plus region blocklist below |
| MiMo-V2.6-Flash Free, Flint 40B Free | may be used to improve the model during the free period | Flint 40B Free keeps data 30 days, others not stated |
| Nemotron 3 Ultra, 3.5 Lightning, 3.5 Super VL Free | NVIDIA trial terms, logged for security and product improvement, not linked to identity | do not submit personal or confidential data |
| Omniroute local gateway | locally configured provider, not covered by Console statements | unmeasured |

Beam Free region blocklist: Angola, Belarus, Cambodia, China including Hong Kong and Macau, Cuba, Democratic Republic of the Congo, Equatorial Guinea, Iran, Iraq, Laos, Libya, Nigeria, North Korea, Russia, Syria, Turkmenistan, Uzbekistan, Venezuela, Crimea, Donetsk and Luhansk regions of Ukraine.

All 8 free routes answered a one-turn probe on 2026-10-23.

## 8. Ranking policy

- R1 capability fit: tools, reasoning controls, modalities, context.
- R2 route preference: first-party host routes before configured upstream free routes before routers.
- R3 recency: newer release and last_updated wins ties.
- R4 data policy preference: zero-retention before 30-day retention before may-use-during-free-period before nvidia-trial-terms before unmeasured.
- R5 review work prefers a different model family than the primary pick.
- R6 fallback chain: beam-free, glm-5.3-flash-free. Flint is for small changes and atomic edits only.
- Override rule: a dated measurement with a stated method replaces a capability pick.

## 9. Provenance and sources

Checked 2026-10-23: https://opencode.ai/v2/docs/console/models/, https://opencode.ai/v2/docs/console/go/, https://opencode.ai/v2/docs/models/, https://models.dev/api.json.

Host commands: `opencode --version`, `opencode auth list`, `opencode debug paths`, `opencode debug config`, `opencode debug agents`, `opencode models`. Catalog tool: models list paging, all=true, limit=100. The tool total and the `opencode models` line count both gave 458.

Reachability: all 8 free refs answered a one-turn `opencode run` probe on 2026-10-23: beam, deepseek-v4.1, flint-40b, glm-5.3, mimo-v2.6, nemotron-3.5-lightning, nemotron-3.5-super-vl, nemotron-3-ultra.

Evidence tags: C host catalog, D models.dev, O OpenCode docs, A auth list, H host probe.

## 10. Stats

<!-- BEGIN AUTO:stats -->
- Catalog: 458 models across 2 providers.
- opencode: 9 models, 8 free.
- omniroute: 449 models, 0 proven free (cost arrays empty, unmeasured).
- Free set: 8 entries.
- opencode: 8 free.
- Run: 2026-10-23-arch-btw, collected 2026-10-23T04:34:16Z.
- models.dev snapshot sha256: 470452beddc8610ffb429fc14a0332e500bb9a6e1662075d6fee7bbe919cd690.
- Enrichment misses: none.
<!-- END AUTO:stats -->
