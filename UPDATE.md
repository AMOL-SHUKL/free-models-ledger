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

## Step 2: catalog pull

Call the models tool with `{ all: true, limit: 100, offset: 0 }` and follow `next` until null. Expected shape today: 5 pages, 458 models, 2 providers.

Cross-check with `opencode models` and count the lines. The two numbers must match.

Free filter: every pricing tier is zero.

```
cost.every(c => c.input === 0 && c.output === 0)
```

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
| AUTO:inventory | one table row per free ref: ctx, out, input modalities, tools, host variants, released, policy class, canonical |
| AUTO:cards | one card per free ref: name, dates, description, limits, modalities, capabilities, variants, family, auth, endpoint, policy, regions, notes |
| AUTO:stats | catalog and free totals, enrichment misses |

Edit narrative sections only when facts moved: Top 3 picks re-derive from the ranking policy, drift lists, privacy matrix, device block for a new device.

## Step 7: commit and push

Commit message format:

```
snapshot(YYYY-MM-DD): +N -M K changed
```

Append the changelog entry first, then commit `LEDGER.md`, `snapshot.json`, `CHANGELOG.md`, `history/`. Push.

## Validation checklist

| Id | Check |
| :--- | :--- |
| V1 | free count in snapshot equals the number of inventory rows |
| V2 | every Top 3 ref exists in the inventory and is zero-price |
| V3 | every free ref appears exactly once in inventory and once in cards |
| V4 | AUTO marker pairs are intact: 3 begin markers, 3 end markers |
| V5 | changelog has an entry for the run date |
| V6 | no secret values anywhere in changed files, env names only |

## Traps

- models.dev descriptions are auto-generated. Quote them, do not treat them as benchmarks. Record conflicts in the drift section.
- Stealth models can be revealed. When a ref disappears and a new ref appears the same day, record the old ref as retired and the new ref as added. Mark the reveal as reported unless the docs state the mapping.
- models.dev cost and host cost have matched in value so far. Trust the host catalog for the free determination.
- OpenRouter free routes and Groq free tiers have account-dependent limits that this host cannot observe. Keep them marked unverified.
- The Console Deprecated models table removes models on rolling dates. Check it monthly.
- `Get-FileHash` is missing in the shell on the Windows device. Hash with .NET SHA256 if a checksum is needed.
- PowerShell 5.1 is the shell on the Windows device. Avoid ternary, `??`, and PS7-only syntax.

## Rebuild

Lost LEDGER.md narrative: rebuild it from the newest snapshot, top to bottom, using the section order in README.md. Lost snapshots: recollect with steps 1 to 3 and start a fresh baseline entry in CHANGELOG.md. A full overhaul is safe; history files are the only irreplaceable content and they are append-only.
