# Free Models Ledger

Declarative record of every zero-price model reachable from OpenCode on this device. Built for daily regeneration by any agent, on any device. Machine-owned sections sit between AUTO markers and are rewritten from `snapshot.json`. Everything else is stable narrative.

**Snapshot 2026-10-17. Run 2026-10-17-laptop-f7fu0ssn. OpenCode 2.0.31. Catalog 489 models, 5 providers. Free 38.**

Sections: 1 Top 3 by category, 2 How to use, 3 Inventory, 4 Cards, 5 Devices, 6 Drift, 7 Privacy, 8 Ranking policy, 9 Provenance, 10 Stats.

## 1. Top 3 by category

Picks are heuristic. They derive from catalog metadata and the routing rules in section 8, not from benchmarks. A dated measurement is the only thing allowed to replace a pick. Valid variant suffixes come from the host catalog per model. An unknown variant is a resolution error.

### 1.1 Primary agentic coding

| Pick | Ref | Why | Call |
| :--- | :--- | :--- | :--- |
| 1 | opencode/beam-free | 501B MoE, 23B active. 1M context, 131k output. Coding and agentic focus. Updated 2026-10-16 | `opencode run --model opencode/beam-free#high "task"` |
| 2 | opencode/glm-5.3-flash-free | Efficient coding agent. Image, video, and pdf input. Zero-retention provider | driver for mixed document and code work |
| 3 | opencode/space-bunny-free | Stealth reasoning model. 1M context, 524k output, image and video input, effort low through max | long agent runs with big outputs |

Alternates: opencode/deepseek-v4.1-flash-free, opencode/longcat-2.5-preview-free, openrouter/thinkingmachines/inkling:free.

Fallback chain when a route throttles: beam-free, glm-5.3-flash-free, space-bunny-free, deepseek-v4.1-flash-free, openrouter/openrouter/free.

### 1.2 Long-context analysis, 1M class

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/beam-free | 1M in, 131k out, text |
| 2 | opencode/nemotron-3-ultra-free | 1M in, 128k out, largest model in the Nemotron 3 line |
| 3 | opencode/space-bunny-free | 1M in, 524k out, image and video |

Alternates: opencode/longcat-2.5-preview-free (1M, image), openrouter/thinkingmachines/inkling:free (1M in, 262k out, audio and image), openrouter/openrouter/pareto-code (2M in, 200k out, no tools, analysis only).

### 1.3 Vision and multimodal

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/nemotron-3.5-super-vl-free | Dedicated vision-language reasoning model, 1M context, image input |
| 2 | opencode/glm-5.3-flash-free | Image, video, and pdf input |
| 3 | opencode/mimo-v2.6-flash-free | Image, audio, and video input. Only first-party free route with audio in |

Alternates: opencode/space-bunny-free (video), opencode/longcat-2.5-preview-free (image), openrouter/nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free (audio and video), openrouter/google/gemma-4-31b-it:free and gemma-4-26b-a4b-it:free (image and video), openrouter/dots-studio/dots-3-note-preview:free (512k in, 460k out), opencode/deepseek-v4.1-flash-free (image).

### 1.4 Fast bulk execution and subagents

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/deepseek-v4.1-flash-free | Flash class, 200k context, 32k out, effort low, high, max |
| 2 | opencode/nemotron-3.5-lightning-free | 262k in and 262k out, built for reliable fast agent chores |
| 3 | opencode/mimo-v2.6-flash-free | Flash class, 200k, multimodal input |

Alternates: groq/groq/compound-mini (131k in, 8k out, no tools, text extraction only), openrouter/liquid/lfm-2.5-2.6b:free (65k), openrouter/nvidia/nemotron-3.5-lightning:free (1M in, 65k out).

### 1.5 Deliberate reasoning and review

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | opencode/beam-free#max | Full effort budget on the largest first-party route |
| 2 | opencode/nemotron-3-ultra-free | Deep reasoner, text, 1M context |
| 3 | openrouter/thinkingmachines/inkling:free#max | Different family for adversarial second opinions, 1M in, 262k out, six effort levels |

Alternates: opencode/big-pickle (stealth reasoning), openrouter/nvidia/nemotron-3-super-120b-a12b:free (effort plus budget tokens), openrouter/nvidia/nemotron-3-ultra-550b-a55b:free (effort plus budget tokens).

### 1.6 Specialists and narrow utility

| Pick | Ref | Why |
| :--- | :--- | :--- |
| 1 | openrouter/nvidia/nemotron-3.5-content-safety:free | Content safety classifier, image input, no tools. Pre and post filtering of agent traffic |
| 2 | openrouter/google/lyria-3-pro-preview | Audio generation. lyria-3-clip-preview for short clips |
| 3 | openrouter/openrouter/free | Free Models Router for emergency fallback when every named route throttles |

Also on record with limits: openrouter/openrouter/pareto-code (2M context router, no tools), openrouter/openrouter/fusion (1M, no tools), openrouter/openrouter/auto (router, text and image output), openrouter/openrouter/bodybuilder (beta), groq/groq/compound (131k, no tools), groq/allam-2-7b (Arabic, 4k context), openrouter/cohere/north-mini-code:free (256k, code), openrouter/poolside/laguna-s-2.1:free and laguna-xs-2.1:free (262k, code), openrouter/xingchen-agi/xing4.0-29b-a4b:free (MoE).

## 2. How to use free models in OpenCode

- Pick for the current session: `/models` in the TUI. The selection applies to the session and does not change config.
- One command run: `opencode run --model opencode/beam-free#high "Refactor parseToken"`.
- Default model: `"model": "opencode/glm-5.3-flash-free"` in `opencode.json`. The root model keeps provider and model only, not a variant.
- Agent model: `"agents": { "reviewer": { "model": "opencode/nemotron-3-ultra-free" } }`. Expanded form accepts providerID, model, and variant. A subagent without its own model inherits the parent session model.
- Command model: `"commands": { "audit": { "template": "Audit $ARGUMENTS.", "model": "opencode/beam-free#high" } }`.
- Variants: append `#name`. Names come from the catalog, listed per model in sections 3 and 4. Unknown variants fail model resolution. Custom variants can be added with a `variants` array on a provider model entry; reasoning controls can be declared with `capabilities.reasoning`.
- Auth: first-party free routes use `OPENCODE_API_KEY` through OpenCode Console or Go. `GROQ_API_KEY` for Groq, `OPENROUTER_API_KEY` for OpenRouter. `/connect` is the TUI path.
- Local zero-cost: OpenCode auto-discovers Ollama, LM Studio, and vLLM on default local addresses. None were listening on this device at collection time.
- Direct HTTP for apps: Console free chat route `https://opencode.ai/inference/openai/v1/chat/completions`, Go `https://opencode.ai/inference/go/openai/v1/chat/completions`. Model lists: `https://opencode.ai/inference/v1/models` and `https://opencode.ai/inference/go/v1/models`.
- Go clients should send a stable session id in `x-opencode-session`. OpenCode does this natively.

## 3. Inventory

<!-- BEGIN AUTO:inventory -->
| Ref | Ctx | Out | Input | Tools | Variants | Released | Policy | Canonical |
| :--- | ---: | ---: | :--- | :--- | :--- | :--- | :--- | :--- |
| opencode/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | retained-30-days | reflection/beam-1.0-501b-a23b |
| opencode/big-pickle | 200,000 | 32,000 | text | yes | - | 2025-10-17 | may-use-during-free-period | - |
| opencode/deepseek-v4.1-flash-free | 200,000 | 32,000 | text, image | yes | low, high, max | 2026-09-10 | zero-retention | deepseek/deepseek-v4.1-flash |
| opencode/glm-5.3-flash-free | 200,000 | 32,000 | text, image, video, pdf | yes | low, high, max | 2026-08-26 | zero-retention | zhipuai/glm-5.3-flash |
| opencode/longcat-2.5-preview-free | 1,000,000 | 131,072 | text, image | yes | - | 2026-09-25 | zero-retention | meituan/longcat-2.5-preview |
| opencode/mimo-v2.6-flash-free | 200,000 | 32,000 | text, image, audio, video | yes | - | 2026-09-22 | may-use-during-free-period | xiaomi/mimo-v2.6-flash |
| opencode/nemotron-3.5-lightning-free | 262,144 | 262,144 | text | yes | - | 2026-08-11 | nvidia-trial-terms | nvidia/nemotron-3.5-lightning |
| opencode/nemotron-3.5-super-vl-free | 1,048,576 | 131,072 | text, image | yes | none, minimal, low, medium, high, xhigh | 2026-10-15 | nvidia-trial-terms | nvidia/nemotron-3.5-super-vl-120b-a12b |
| opencode/nemotron-3-ultra-free | 1,000,000 | 128,000 | text | yes | - | 2026-06-04 | nvidia-trial-terms | nvidia/nemotron-3-ultra-550b-a55b |
| opencode/space-bunny-free | 1,048,576 | 524,288 | text, image, video | yes | low, medium, high, xhigh, max | 2026-09-23 | zero-retention | - |
| opencode-go/beam-free | 1,048,576 | 131,072 | text | yes | low, medium, high, xhigh, max | 2026-10-05 | retained-30-days | reflection/beam-1.0-501b-a23b |
| opencode-go/longcat-2.5-preview-free | 1,000,000 | 131,072 | text, image | yes | - | 2026-09-25 | zero-retention | meituan/longcat-2.5-preview |
| openrouter/cohere/north-mini-code:free | 256,000 | 64,000 | text | yes | none, thinking | 2026-06-17 | upstream-unverified | - |
| openrouter/dots-studio/dots-3-note-preview:free | 512,000 | 460,800 | text, image | yes | none, thinking | 2026-08-14 | upstream-unverified | - |
| openrouter/google/gemma-4-26b-a4b-it:free | 262,144 | 32,768 | image, text, video | yes | none, thinking | 2026-04-02 | upstream-unverified | google/gemma-4-26b-a4b-it |
| openrouter/google/gemma-4-31b-it:free | 262,144 | 32,768 | image, text, video | yes | none, thinking | 2026-04-02 | upstream-unverified | google/gemma-4-31b-it |
| openrouter/google/lyria-3-clip-preview | 1,048,576 | 65,536 | text, image | no | - | 2026-03-25 | upstream-unverified | google/lyria-3-clip-preview |
| openrouter/google/lyria-3-pro-preview | 1,048,576 | 65,536 | text, image | no | - | 2026-03-25 | upstream-unverified | google/lyria-3-pro-preview |
| openrouter/liquid/lfm-2.5-2.6b:free | 65,536 | 8,192 | text | yes | - | 2026-08-11 | upstream-unverified | - |
| openrouter/nvidia/nemotron-3.5-content-safety:free | 128,000 | 8,192 | text, image | no | none, thinking | 2026-06-04 | upstream-unverified | nvidia/nemotron-3.5-content-safety |
| openrouter/nvidia/nemotron-3.5-lightning:free | 1,000,000 | 65,536 | text | yes | none, thinking | 2026-08-11 | upstream-unverified | nvidia/nemotron-3.5-lightning |
| openrouter/nvidia/nemotron-3.5-super-vl:free | 1,000,000 | 131,072 | text, image, video | yes | none, thinking | 2026-10-15 | upstream-unverified | - |
| openrouter/nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free | 256,000 | 65,536 | text, image, video, audio | yes | none, high, max | 2026-04-28 | upstream-unverified | nvidia/nemotron-3-nano-omni-30b-a3b-reasoning |
| openrouter/nvidia/nemotron-3-super-120b-a12b:free | 262,144 | 235,929 | text | yes | none, low, medium | 2026-03-11 | upstream-unverified | nvidia/nemotron-3-super-120b-a12b |
| openrouter/nvidia/nemotron-3-ultra-550b-a55b:free | 1,000,000 | 65,536 | text | yes | none, medium, high | 2026-06-04 | upstream-unverified | nvidia/nemotron-3-ultra-550b-a55b |
| openrouter/openrouter/auto | 2,000,000 | 2,000,000 | text, image, audio, pdf, video | yes | - | 2023-11-08 | upstream-unverified | - |
| openrouter/openrouter/bodybuilder | 128,000 | 128,000 | text | no | - | 2025-12-05 | upstream-unverified | - |
| openrouter/openrouter/free | 200,000 | 8,000 | text, image | yes | - | 2026-02-01 | upstream-unverified | - |
| openrouter/openrouter/fusion | 1,000,000 | 128,000 | text | no | - | 2026-06-13 | upstream-unverified | - |
| openrouter/openrouter/pareto-code | 2,000,000 | 200,000 | text | no | - | 2026-04-21 | upstream-unverified | - |
| openrouter/poolside/laguna-s-2.1:free | 262,144 | 32,768 | text | yes | none, thinking | 2026-07-21 | upstream-unverified | - |
| openrouter/poolside/laguna-xs-2.1:free | 262,144 | 32,768 | text | yes | none, thinking | 2026-07-02 | upstream-unverified | poolside/laguna-xs-2.1 |
| openrouter/thinkingmachines/inkling:free | 1,048,576 | 262,144 | text, image, audio | yes | none, minimal, low, medium, high, max | 2026-07-15 | upstream-unverified | thinkingmachines/inkling |
| openrouter/thinkingmachines/inkling-small:free | 1,048,576 | 262,144 | text, image, audio | yes | none, minimal, low, medium, high, max | 2026-07-30 | upstream-unverified | thinkingmachines/inkling-small |
| openrouter/xingchen-agi/xing4.0-29b-a4b:free | 262,144 | 235,929 | text | yes | none, thinking | 2026-10-16 | upstream-unverified | - |
| groq/allam-2-7b | 4,096 | 4,096 | text | no | - | 2025-01-23 | upstream-unverified | sdaia/allam-2-7b |
| groq/groq/compound | 131,072 | 8,192 | text | no | - | 2025-09-04 | upstream-unverified | - |
| groq/groq/compound-mini | 131,072 | 8,192 | text | no | - | 2025-09-04 | upstream-unverified | - |
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

### opencode/big-pickle
- Name: Big Pickle. Status: active. Released: 2025-10-17. Last updated: 2025-10-17. Knowledge cutoff: 2025-01.
- Description: Reasoning model for deliberate analysis, multi-step problem solving, and tool use
- Context: 200,000 in, 32,000 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: no. Interleaved field: reasoning_content.
- Family: big-pickle. Canonical: -. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: may-use-during-free-period. During the free period, collected data may be used to improve the model.

### opencode/deepseek-v4.1-flash-free
- Name: DeepSeek V4.1 Flash Free. Status: active. Released: 2026-09-10. Last updated: 2026-09-10. Knowledge cutoff: 2025-05.
- Description: DeepSeek V4.1 Flash model for reasoning and agentic coding
- Context: 200,000 in, 32,000 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, high, max. Host variants: low, high, max.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: deepseek-flash. Canonical: deepseek/deepseek-v4.1-flash. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: zero-retention. Prompts and responses are not used for training. Console states a zero-retention policy for these routes.

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

### opencode/longcat-2.5-preview-free
- Name: LongCat 2.5 Preview Free. Status: active. Released: 2026-09-25. Last updated: 2026-09-25. Knowledge cutoff: -.
- Description: Meituan's multimodal reasoning model for coding and agent tasks, with image understanding and a 1M-token context window
- Context: 1,000,000 in, 131,072 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: reasoning_content.
- Family: longcat. Canonical: meituan/longcat-2.5-preview. In Console free list: yes.
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
- Name: Nemotron 3.5 Lightning Free. Status: active. Released: 2026-08-11. Last updated: 2026-08-11. Knowledge cutoff: -.
- Description: Fast NVIDIA Nemotron MoE for reliable agentic tasks across enterprise workloads
- Context: 262,144 in, 262,144 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron-free. Canonical: nvidia/nemotron-3.5-lightning. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.

### opencode/nemotron-3.5-super-vl-free
- Name: Nemotron 3.5 Super VL Free. Status: active. Released: 2026-10-15. Last updated: 2026-10-15. Knowledge cutoff: -.
- Description: NVIDIA vision-language reasoning model for text, image, and video understanding and agentic tool use
- Context: 1,048,576 in, 131,072 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, minimal, low, medium, high, xhigh. Host variants: none, minimal, low, medium, high, xhigh.
- Structured output: -. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron-free. Canonical: nvidia/nemotron-3.5-super-vl-120b-a12b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.

### opencode/nemotron-3-ultra-free
- Name: Nemotron 3 Ultra Free. Status: active. Released: 2026-06-04. Last updated: 2026-06-04. Knowledge cutoff: 2026-02.
- Description: Largest Nemotron 3 model for maximum open-weight reasoning and agent accuracy
- Context: 1,000,000 in, 128,000 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: nemotron-free. Canonical: nvidia/nemotron-3-ultra-550b-a55b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: nvidia-trial-terms. NVIDIA free endpoint. Trial use only. Do not submit personal or confidential data. Use is logged for security and product improvement and is not linked to identity.

### opencode/space-bunny-free
- Name: Space Bunny Free. Status: active. Released: 2026-09-23. Last updated: 2026-09-23. Knowledge cutoff: -.
- Description: Anonymous preview reasoning model for coding, agentic tasks, tool use, and multimodal input
- Context: 1,048,576 in, 524,288 out.
- Modalities: input text, image, video. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, medium, high, xhigh, max. Host variants: low, medium, high, xhigh, max.
- Structured output: -. Temperature: -. Attachment: yes. Open weights: no. Interleaved field: reasoning_content.
- Family: -. Canonical: -. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/openai/v1/chat/completions (console). SDK: @ai-sdk/openai-compatible.
- Policy: zero-retention. Prompts and responses are not used for training. Console states a zero-retention policy for these routes.

### opencode-go/beam-free
- Name: Beam Free. Status: active. Released: 2026-10-05. Last updated: 2026-10-16. Knowledge cutoff: 2026-06-30.
- Description: Sparse mixture-of-experts reasoning model with 23B active parameters for coding and agentic workflows
- Context: 1,048,576 in, 131,072 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort low, medium, high, xhigh, max. Host variants: low, medium, high, xhigh, max.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: reasoning_content.
- Family: beam. Canonical: reflection/beam-1.0-501b-a23b. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/go/openai/v1/chat/completions (go). SDK: @ai-sdk/openai-compatible.
- Policy: retained-30-days. Not used for training. Data is retained for 30 days for safety reasons.
- Region restriction: yes. Angola, Belarus, Cambodia, China including Hong Kong and Macau, Cuba, Democratic Republic of the Congo, Equatorial Guinea, Iran, Iraq, Laos, Libya, Nigeria, North Korea, Russia, Syria, Turkmenistan, Uzbekistan, Venezuela, Crimea, Donetsk and Luhansk regions of Ukraine.
- Notes: OpenCode Go route mirror of opencode/beam-free. Free models keep working after Go usage limits..

### opencode-go/longcat-2.5-preview-free
- Name: LongCat 2.5 Preview Free. Status: active. Released: 2026-09-25. Last updated: 2026-09-25. Knowledge cutoff: -.
- Description: Meituan's multimodal reasoning model for coding and agent tasks, with image understanding and a 1M-token context window
- Context: 1,000,000 in, 131,072 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: reasoning_content.
- Family: longcat. Canonical: meituan/longcat-2.5-preview. In Console free list: yes.
- Auth: OPENCODE_API_KEY. Endpoint: https://opencode.ai/inference/go/openai/v1/chat/completions (go). SDK: @ai-sdk/openai-compatible.
- Policy: zero-retention. Prompts and responses are not used for training. Console states a zero-retention policy for these routes.
- Notes: OpenCode Go route mirror of opencode/longcat-2.5-preview-free. Listed as limited time in Go..

### openrouter/cohere/north-mini-code:free
- Name: North Mini Code (free). Status: active. Released: 2026-06-17. Last updated: 2026-06-17. Knowledge cutoff: -.
- Description: Cohere coding model for practical software engineering and agentic edits
- Context: 256,000 in, 64,000 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: north. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/dots-studio/dots-3-note-preview:free
- Name: Dots3-Note Preview (free). Status: active. Released: 2026-08-14. Last updated: 2026-08-14. Knowledge cutoff: -.
- Description: Multimodal reasoning model for visual analysis, planning, and tool use
- Context: 512,000 in, 460,800 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: -.
- Family: -. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/google/gemma-4-26b-a4b-it:free
- Name: Gemma 4 26B A4B  (free). Status: active. Released: 2026-04-02. Last updated: 2026-04-02. Knowledge cutoff: -.
- Description: Open Gemma instruction model for efficient chat and self-hosted deployments
- Context: 262,144 in, 32,768 out.
- Modalities: input image, text, video. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: gemma. Canonical: google/gemma-4-26b-a4b-it. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/google/gemma-4-31b-it:free
- Name: Gemma 4 31B (free). Status: active. Released: 2026-04-02. Last updated: 2026-04-02. Knowledge cutoff: -.
- Description: Largest Gemma 4 instruction model for open, self-hosted chat and reasoning
- Context: 262,144 in, 32,768 out.
- Modalities: input image, text, video. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: gemma. Canonical: google/gemma-4-31b-it. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/google/lyria-3-clip-preview
- Name: Lyria 3 Clip Preview. Status: active. Released: 2026-03-25. Last updated: 2026-03-25. Knowledge cutoff: -.
- Description: Speech generation model for controllable voice, narration, and audio delivery
- Context: 1,048,576 in, 65,536 out.
- Modalities: input text, image. output text, audio.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: -.
- Family: lyria. Canonical: google/lyria-3-clip-preview. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Audio generation, short clips..

### openrouter/google/lyria-3-pro-preview
- Name: Lyria 3 Pro Preview. Status: active. Released: 2026-03-25. Last updated: 2026-03-25. Knowledge cutoff: -.
- Description: Speech generation model for controllable voice, narration, and audio delivery
- Context: 1,048,576 in, 65,536 out.
- Modalities: input text, image. output text, audio.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: -.
- Family: lyria. Canonical: google/lyria-3-pro-preview. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Audio generation..

### openrouter/liquid/lfm-2.5-2.6b:free
- Name: LFM2.5-2.6B (free). Status: active. Released: 2026-08-11. Last updated: 2026-08-11. Knowledge cutoff: -.
- Description: Free provider route for experiments, demos, and cost-sensitive chat workloads
- Context: 65,536 in, 8,192 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: liquid. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/nvidia/nemotron-3.5-content-safety:free
- Name: Nemotron 3.5 Content Safety (free). Status: active. Released: 2026-06-04. Last updated: 2026-06-04. Knowledge cutoff: -.
- Description: Safety model for policy screening, moderation, and risk-aware routing workflows
- Context: 128,000 in, 8,192 out.
- Modalities: input text, image. output text.
- Tools: no. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: nvidia/nemotron-3.5-content-safety. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Content safety classifier..

### openrouter/nvidia/nemotron-3.5-lightning:free
- Name: Nemotron 3.5 Lightning (free). Status: active. Released: 2026-08-11. Last updated: 2026-08-11. Knowledge cutoff: -.
- Description: Nemotron model for efficient reasoning, coding, and specialized AI agents
- Context: 1,000,000 in, 65,536 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: nvidia/nemotron-3.5-lightning. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/nvidia/nemotron-3.5-super-vl:free
- Name: Nemotron 3.5 Super VL (free). Status: active. Released: 2026-10-15. Last updated: 2026-10-15. Knowledge cutoff: -.
- Description: Nemotron multimodal model for visual reasoning and agentic AI workflows
- Context: 1,000,000 in, 131,072 out.
- Modalities: input text, image, video. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free
- Name: Nemotron 3 Nano Omni (free). Status: active. Released: 2026-04-28. Last updated: 2026-04-28. Knowledge cutoff: -.
- Description: Open Nemotron omni model combining reasoning with text, vision, and audio
- Context: 256,000 in, 65,536 out.
- Modalities: input text, image, video, audio. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle; budget_tokens. Host variants: none, high, max.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: nvidia/nemotron-3-nano-omni-30b-a3b-reasoning. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Small omni model, audio and video input..

### openrouter/nvidia/nemotron-3-super-120b-a12b:free
- Name: Nemotron 3 Super (free). Status: active. Released: 2026-03-11. Last updated: 2026-03-11. Knowledge cutoff: -.
- Description: Nemotron middle tier for collaborative agents and high-volume reasoning workloads
- Context: 262,144 in, 235,929 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, low, medium; budget_tokens. Host variants: none, low, medium.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: nvidia/nemotron-3-super-120b-a12b. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/nvidia/nemotron-3-ultra-550b-a55b:free
- Name: Nemotron 3 Ultra (free). Status: active. Released: 2026-06-04. Last updated: 2026-06-04. Knowledge cutoff: -.
- Description: Largest Nemotron 3 model for maximum open-weight reasoning and agent accuracy
- Context: 1,000,000 in, 65,536 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, medium, high; budget_tokens. Host variants: none, medium, high.
- Structured output: no. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: nemotron. Canonical: nvidia/nemotron-3-ultra-550b-a55b. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/openrouter/auto
- Name: Auto Router. Status: active. Released: 2023-11-08. Last updated: 2023-11-08. Knowledge cutoff: -.
- Description: Image model for prompt-driven generation, editing, and visual design workflows
- Context: 2,000,000 in, 2,000,000 out.
- Modalities: input text, image, audio, pdf, video. output text, image.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: -.
- Family: auto. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: General router; catalog lists text and image output modalities..

### openrouter/openrouter/bodybuilder
- Name: Body Builder (beta). Status: active. Released: 2025-12-05. Last updated: 2025-12-05. Knowledge cutoff: -.
- Description: Preview model for early access evaluation, prototyping, and compatibility testing
- Context: 128,000 in, 128,000 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: no. Temperature: no. Attachment: no. Open weights: no. Interleaved field: -.
- Family: -. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Beta..

### openrouter/openrouter/free
- Name: Free Models Router. Status: active. Released: 2026-02-01. Last updated: 2026-02-01. Knowledge cutoff: -.
- Description: Multimodal reasoning model for visual analysis, planning, and tool use
- Context: 200,000 in, 8,000 out.
- Modalities: input text, image. output text.
- Tools: yes. Reasoning: yes. Reasoning options: -. Host variants: -.
- Structured output: yes. Temperature: yes. Attachment: yes. Open weights: no. Interleaved field: -.
- Family: -. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Router that selects among free models..

### openrouter/openrouter/fusion
- Name: Fusion. Status: active. Released: 2026-06-13. Last updated: 2026-06-13. Knowledge cutoff: -.
- Description: General-purpose chat model for instruction following, writing, and analysis
- Context: 1,000,000 in, 128,000 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: no. Temperature: no. Attachment: no. Open weights: no. Interleaved field: -.
- Family: -. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Chat model..

### openrouter/openrouter/pareto-code
- Name: Pareto Code Router. Status: active. Released: 2026-04-21. Last updated: 2026-04-21. Knowledge cutoff: -.
- Description: Coding model for repository understanding, refactors, and agentic engineering tasks
- Context: 2,000,000 in, 200,000 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: no. Temperature: no. Attachment: no. Open weights: no. Interleaved field: -.
- Family: -. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Code router; large-context fallback..

### openrouter/poolside/laguna-s-2.1:free
- Name: Laguna S 2.1 (free). Status: active. Released: 2026-07-21. Last updated: 2026-07-21. Knowledge cutoff: -.
- Description: Free provider route for experiments, demos, and cost-sensitive chat workloads
- Context: 262,144 in, 32,768 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: laguna-s. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/poolside/laguna-xs-2.1:free
- Name: Laguna XS 2.1 (free). Status: active. Released: 2026-07-02. Last updated: 2026-07-02. Knowledge cutoff: -.
- Description: Free provider route for experiments, demos, and cost-sensitive chat workloads
- Context: 262,144 in, 32,768 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: no. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: laguna. Canonical: poolside/laguna-xs-2.1. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/thinkingmachines/inkling:free
- Name: Inkling (free). Status: active. Released: 2026-07-15. Last updated: 2026-07-15. Knowledge cutoff: -.
- Description: Multimodal reasoning model for visual analysis, planning, and tool use
- Context: 1,048,576 in, 262,144 out.
- Modalities: input text, image, audio. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, minimal, low, medium, high, max. Host variants: none, minimal, low, medium, high, max.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: ling. Canonical: thinkingmachines/inkling. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/thinkingmachines/inkling-small:free
- Name: Inkling Small (free). Status: active. Released: 2026-07-30. Last updated: 2026-07-30. Knowledge cutoff: -.
- Description: Multimodal reasoning model for visual analysis, planning, and tool use
- Context: 1,048,576 in, 262,144 out.
- Modalities: input text, image, audio. output text.
- Tools: yes. Reasoning: yes. Reasoning options: effort none, minimal, low, medium, high, max. Host variants: none, minimal, low, medium, high, max.
- Structured output: no. Temperature: yes. Attachment: yes. Open weights: yes. Interleaved field: -.
- Family: ling. Canonical: thinkingmachines/inkling-small. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### openrouter/xingchen-agi/xing4.0-29b-a4b:free
- Name: Xing4.0 29B A4B (free). Status: active. Released: 2026-10-16. Last updated: 2026-10-16. Knowledge cutoff: -.
- Description: Free provider route for experiments, demos, and cost-sensitive chat workloads
- Context: 262,144 in, 235,929 out.
- Modalities: input text. output text.
- Tools: yes. Reasoning: yes. Reasoning options: toggle. Host variants: none, thinking.
- Structured output: yes. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: agi. Canonical: -. In Console free list: no.
- Auth: OPENROUTER_API_KEY. Endpoint: https://openrouter.ai/api/v1 (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: OpenRouter free route. Rate limits depend on the OpenRouter account..

### groq/allam-2-7b
- Name: ALLaM-2-7b. Status: active. Released: 2025-01-23. Last updated: 2025-01-23. Knowledge cutoff: -.
- Description: ALLaM-2-7b instruction tuned model by SDAIA
- Context: 4,096 in, 4,096 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: no. Open weights: yes. Interleaved field: -.
- Family: -. Canonical: sdaia/allam-2-7b. In Console free list: no.
- Auth: GROQ_API_KEY. Endpoint: - (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Groq free-tier route. Limits depend on the Groq account..

### groq/groq/compound
- Name: Compound. Status: active. Released: 2025-09-04. Last updated: 2025-09-04. Knowledge cutoff: -.
- Description: General-purpose chat model for instruction following, writing, and analysis
- Context: 131,072 in, 8,192 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: no. Open weights: no. Interleaved field: -.
- Family: groq. Canonical: -. In Console free list: no.
- Auth: GROQ_API_KEY. Endpoint: - (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Groq free-tier route. Limits depend on the Groq account..

### groq/groq/compound-mini
- Name: Compound Mini. Status: active. Released: 2025-09-04. Last updated: 2025-09-04. Knowledge cutoff: -.
- Description: Efficient model for low-latency assistance, extraction, and routine automation
- Context: 131,072 in, 8,192 out.
- Modalities: input text. output text.
- Tools: no. Reasoning: no. Reasoning options: -. Host variants: -.
- Structured output: -. Temperature: yes. Attachment: no. Open weights: no. Interleaved field: -.
- Family: groq. Canonical: -. In Console free list: no.
- Auth: GROQ_API_KEY. Endpoint: - (upstream). SDK: @ai-sdk/openai-compatible.
- Policy: upstream-unverified. Route policy is not covered by OpenCode Console statements. Verify with the upstream provider.
- Notes: Groq free-tier route. Limits depend on the Groq account..

<!-- END AUTO:cards -->

## 5. Devices

### LAPTOP-F7FU0SSN, Windows, recorded 2026-10-17

- OpenCode 2.0.31 via the pi-node wrapper. Config source: `C:\Users\Amol\.config\opencode`.
- Credentials by environment variable name: OPENCODE_API_KEY (Console and Go), GROQ_API_KEY, MISTRAL_API_KEY, OPENROUTER_API_KEY. No values recorded.
- Local model servers on 11434, 1234, 8000: all closed.
- Agents: build and plan primary, explore and general subagents, hidden system agents present.

## 6. Drift watchlist

Documented free offers absent from this device catalog today: ll-alpha-free, exo-free, mimo-v2.5-free, muse-spark-1.3-contributor-free, jev-1.13-free (System One endpoint, not a chat model).

Catalog entries outside the Console free list: the OpenRouter routes are OpenRouter's own free offers. Groq entries are Groq free-tier routes. Routers, the Lyria audio models, and the safety classifier are priced free in the catalog but are not chat agent drivers.

Open questions carried forward: Console free model rate limits are not stated in the docs. OpenRouter free route limits depend on account credits. Groq free-tier limits are unverified from this device. Deprecated models are removed on rolling dates.

Watch rules: re-check the Console free models list and the Go model list daily. Treat a model as removed only after it is absent from the host catalog for two runs. Never reclassify a route as free because a blog says so; the catalog is the source of truth.

## 7. Privacy and data handling

Console states: all models hosted in the US and EU, zero-retention default, no training on data. Exceptions, quoted and dated 2026-10-17:

| Scope | Training | Retention |
| :--- | :--- | :--- |
| Console default | not used | zero retention |
| GLM-5.3-Flash Free, DeepSeek V4.1 Flash Free, Space Bunny Free, LongCat 2.5 Preview Free | not used | zero retention |
| Beam Free, both routes | not used | 30 days for safety |
| Big Pickle, MiMo-V2.6-Flash Free | may be used to improve the model during the free period | not stated |
| Nemotron 3 Ultra, 3.5 Lightning, 3.5 Super VL Free first-party routes | NVIDIA trial terms, logged for security and product improvement, not linked to identity | do not submit personal or confidential data |
| OpenRouter free routes | upstream policy, not covered by Console statements | account-dependent |
| Groq free routes | upstream policy | account-dependent |

Beam Free region blocklist: Angola, Belarus, Cambodia, China including Hong Kong and Macau, Cuba, Democratic Republic of the Congo, Equatorial Guinea, Iran, Iraq, Laos, Libya, Nigeria, North Korea, Russia, Syria, Turkmenistan, Uzbekistan, Venezuela, Crimea, Donetsk and Luhansk regions of Ukraine.

Documented but not on this device today, privacy rows for the watchlist: LL Alpha Free retains 30 days and may use data. Exo Free may use data. MiMo-V2.5 Free may use data. Muse Spark 1.3 Contributor trains Meta models. Jev 1.13 Free is limited-time.

## 8. Ranking policy

- R1 capability fit: tools, reasoning controls, modalities, context.
- R2 route preference: first-party host routes, Console and Go, before configured upstream free routes, Groq and OpenRouter, before routers.
- R3 recency: newer release and last_updated wins ties.
- R4 data policy preference: zero-retention before 30-day retention before may-use before upstream-unverified.
- R5 review work prefers a different model family than the primary pick.
- R6 fallback chain: beam-free, glm-5.3-flash-free, space-bunny-free, deepseek-v4.1-flash-free, openrouter/openrouter/free.
- Override rule: only a dated measurement with a stated method replaces a pick. Record it in the changelog and in the card notes.

## 9. Provenance and sources

Checked 2026-10-17: https://opencode.ai/v2/docs/console/models/, https://opencode.ai/v2/docs/console/go/, https://opencode.ai/v2/docs/models/, https://opencode.ai/v2/docs/agents/, https://opencode.ai/v2/docs/commands/, https://models.dev/api.json.

Host commands: `opencode --version`, `opencode auth list`, `opencode debug paths`, `opencode debug config`, `opencode debug agents`, `opencode models`. Catalog tool: models list paging, all=true, limit=100, offset 0 through 4.

Evidence tags: C host catalog, D models.dev, O OpenCode docs, A auth list, H host probe.

## 10. Stats

<!-- BEGIN AUTO:stats -->
- Catalog: 489 models across 5 providers.
- opencode: 42 models, 10 free.
- groq: 12 models, 3 free.
- mistral: 18 models, 0 free.
- opencode-go: 29 models, 2 free.
- openrouter: 388 models, 23 free.
- Free set: 38 entries.
- opencode: 10 free.
- groq: 3 free.
- opencode-go: 2 free.
- openrouter: 23 free.
- Run: 2026-10-17-laptop-f7fu0ssn, collected 2026-10-17T08:42:32Z.
- models.dev snapshot sha256: cc08621a7a825bdb902577b90c6302b2a3c524cc2cb96421a4dfe04b080d5e6c.
- Enrichment misses: none.
<!-- END AUTO:stats -->

