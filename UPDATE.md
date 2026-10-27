# Update protocol

Agent-only. No scripts live in this repo. Any agent, on any device, follows the steps in order. Every step is re-runnable on its own. If a run stops midway, the repo stays valid: finish at the last complete step, then commit what exists with a partial note in CHANGELOG.md.

## Preconditions

- `git pull --ff-only` in the repo root. Never collect on a dirty tree.
- Confirm today's date and the device name. On Windows, `$env:COMPUTERNAME`.

## Step 1: host probe

| Command | Use |
| :--- | :--- |
| `opencode --version` | version line for the device block |
| `opencode auth list` | provider, credential name, method. Record names only, never values |
| `opencode debug paths` | path table for the device block |
| `opencode debug config` | config source list |
| `opencode debug agents` | agent roster: id, mode, hidden |
| port probe on 11434, 1234, 8000 | local zero-cost detection: Ollama, LM Studio, vLLM |

## Step 1b: gateway probe

Run these only when the gateway answers. Use the key from the gateway env file, never a copied value.

| Command | Use |
| :--- | :--- |
| `omniroute health` | version, uptime, request count |
| `omniroute providers list` | configured connections, auth type, active flag |
| `omniroute providers status` | expiry dates, test status, cooldown |
| `omniroute providers test-all --json` | per-connection probe. OAuth and no-auth entries report "not applicable" |
| `omniroute quota` | provider quota, if the gateway tracks it |
| `curl /v1/models` with a bearer key | model entries per upstream. Strip ANSI before parsing |

Record two counts separately. Connections configured, and providers exposing models. The sets differ.

A connection is usable only after a live chat call returns content. A 200 with empty content counts as unusable until retried once.

## Step 2: catalog pull

Call the models tool with `{ all: true, limit: 100, offset: 0 }` and follow `next` until null. Expected shape today: 213 refs.

Cross-check with `opencode models` and count the lines. Today that gives 217. The CLI is authoritative for routing, because it lists what `opencode run` resolves.

A gap between the two counts is expected. Record it in section 13. Never rebuild the free set from the tool alone. The tool has already dropped three free refs.

Free filter: every pricing tier must be present and zero. An empty cost array is not proof of zero price.

```
cost.length > 0 && cost.every(c => c.input === 0 && c.output === 0)
```

Dedupe by model id, not by ref. One model appears under `opencode/` and under each Console account prefix. Count distinct models for the free total. Count refs only for quota buckets.

## Step 3: enrichment

Fetch `https://models.dev/api.json`. Join on provider id plus model id, stripping the provider prefix from the ref. Copy: description, family, canonical_model_id, release_date, last_updated, knowledge, limit, modalities, tool_call, reasoning, reasoning_options, structured_output, interleaved, attachment, temperature, open_weights.

If a free model has no models.dev entry, record the ref under `validation.missingEnrichment` and leave the fields null. Never invent values.

## Step 4: diff

Compare the new free set against the newest file in `history/`.

| Class | Rule |
| :--- | :--- |
| added | ref present today, absent in the previous snapshot |
| removed | ref present in the previous snapshot, absent today |
| changed | same ref, any of: limits, modalities, toolCall, reasoningOptions, hostVariants, policyClass, releasedDate, lastUpdated, status |

Write the result as a short list. A no-change day still gets a changelog line.

## Step 5: snapshot

Write `history/<YYYY-MM-DD>-<device>.snapshot.json` and copy it to `snapshot.json`. Device is the lowercase hostname. If the same day already has a file from another device, keep both and suffix `-2`. The newest collection wins for `snapshot.json`. Prior files are never deleted.

Schema: `schemaVersion`, `runId`, `runDate`, `collectedAt`, `catalogPulledAt`, `definitions`, `device`, `host`, `catalog`, `freeTotals`, `free`, `drift`, `sources`, `validation`. Copy field shapes from the current `snapshot.json`.

## Step 6: render

Regenerate only these regions of LEDGER.md:

| Region | Content |
| :--- | :--- |
| AUTO:inventory | one table row per distinct free model: ctx, out, input modalities, tools, host variants, released, policy class, canonical |
| AUTO:cards | one card per shortlisted free model: name, dates, description, limits, modalities, capabilities, policy, route |
| AUTO:stats | distinct free models, free refs, per-prefix counts, archive counts, mode |

Edit narrative sections only when facts moved: drift lists, privacy matrix, device block.

These sections are judgment, not machine output. Never regenerate them from a script.

| Section | Owner | Rule |
| :--- | :--- | :--- |
| 2 Domains | human | every row needs an auth type and a state |
| 3 Shortlist | human or measured probe | eight entries maximum. A ninth needs a measured class the eight cannot serve |
| 4 Gateway | human | two tables. Connections and catalog-only providers. A row without a probe result is unverified |
| 5 Budget | human | shares sum to 100 percent |
| 6 Uncapped and expiry | human | every connection with an expiry date gets a row. Re-auth is a user action |
| 7 Archive | human | append only. A model leaves with a measurement in section 14 |

## Step 7: commit and push

Commit message format:

```
snapshot(YYYY-MM-DD): +N -M K changed
```

Append the changelog entry first, then commit `LEDGER.md`, `snapshot.json`, `CHANGELOG.md`, `history/`. Push.

## Validation checklist

| Id | Check |
| :--- | :--- |
| V1 | distinct free model count equals the inventory row count |
| V2 | every section 2 shortlist ref resolves in `opencode models` and is zero-price |
| V3 | every free model appears once in inventory, and once in either cards or archive |
| V4 | AUTO marker pairs are intact: 3 begin markers, 3 end markers |
| V5 | changelog has an entry for the run date |
| V6 | no secret values anywhere in changed files, env names only |
| V7 | every archive entry states a reason, not just a name |
| V8 | every connection with an expiry date appears in section 6 |
| V9 | tool and CLI counts are both recorded, with the delta named |
| V10 | the three routed Console account prefixes carry equal model counts |
| V11 | every gateway connection in section 4 carries a state and a probe result |
| V12 | connection count and model-provider count are recorded separately |
| V13 | every shortlist gateway ref has a dated live chat result in section 14 |

## Traps

- models.dev descriptions are auto-generated. Quote them, do not treat them as benchmarks. Record conflicts in the drift section.
- Stealth models can be revealed. When a ref disappears and a new ref appears the same day, record the old ref as retired and the new ref as added. Mark the reveal as reported unless the docs state the mapping.
- models.dev cost and host cost have matched in value so far. Trust the host catalog for the free determination.
- OpenRouter free routes and Groq free tiers have account-dependent limits that this host cannot observe. Keep them marked unverified.
- The Console Deprecated models table removes models on rolling dates. Check it monthly.
- `Get-FileHash` is missing in the shell on the Windows device. Hash with .NET SHA256 if a checksum is needed.
- PowerShell 5.1 is the shell on the Windows device. Avoid ternary, `??`, and PS7-only syntax.

## Rebuild

Lost LEDGER.md narrative: rebuild it from the newest snapshot, top to bottom, using the section order in LEDGER.md itself. Lost snapshots: recollect with steps 1 to 3 and start a fresh baseline entry in CHANGELOG.md. A full overhaul is safe; history files are the only irreplaceable content and they are append-only.
