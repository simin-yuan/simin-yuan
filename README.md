# Simin Yuan

**Founder, Light Emerge** · Agent reliability research and engineering

I find where AI agents fail — runaway loops, duplicate execution, truncated tool calls —
measure it reproducibly, and fix it upstream.

[lightemerge.com](https://lightemerge.com) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ) · [ORCID](https://orcid.org/0009-0002-6843-3390) · [LinkedIn](https://www.linkedin.com/in/simin-yuan-2845a03bb)

## Highlights

- Six paper acceptances across NeurIPS 2026 workshops, on how LLM agents fail and recover
- 3 plugins added to the official Hermes Agent plugin catalog (4 merged PRs)
- Bugs found and fixes filed in crewAI, Hugging Face smolagents, LangChain.js, OpenAI Codex and SchemaStore
- Credited on 3 security advisories in nexus-memory — 1 high, 2 moderate ([GHSA-2h94](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-2h94-xx3x-chrr), [GHSA-m446](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-m446-mvgq-w52p), [GHSA-455p](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-455p-688x-56r8))

## Selected upstream contributions

Sorted by weight: named organization, a quantified finding, a fix.

| Project | What I found or fixed | Status |
|---|---|---|
| [crewAI](https://github.com/crewAIInc/crewAI) | Three retry layers stack, so **one 429 becomes 27 identical requests** | Reported [#8332](https://github.com/crewAIInc/crewAI/issues/8332) · fix [#8333](https://github.com/crewAIInc/crewAI/pull/8333) open |
| [Hugging Face smolagents](https://github.com/huggingface/smolagents) | Client retries and `ApiModel` retries multiply — **one rate limit becomes 9 requests**; `Retrying` ignores `Retry-After` and the first retry already waits 2–3× `wait_seconds` | Reported [#3034](https://github.com/huggingface/smolagents/issues/3034), [#3037](https://github.com/huggingface/smolagents/issues/3037) · fix [#3036](https://github.com/huggingface/smolagents/pull/3036) open |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | **3 plugins added to the official plugin catalog** — degeneracy-guard, provenance-gate, skill-lineage; core fixes filed for repetition loops, for a stream hook that cannot ask a response to stop, and for a missing `platform` in the tool-path hooks | **4 merged (UTC), all by @teknium1** ([#141341](https://github.com/NousResearch/hermes-agent/pull/141341) 2026-10-20, [#141935](https://github.com/NousResearch/hermes-agent/pull/141935) 2026-10-20, [#142381](https://github.com/NousResearch/hermes-agent/pull/142381) 2026-10-24, [#144339](https://github.com/NousResearch/hermes-agent/pull/144339) 2026-10-25) · 4 core PRs open ([#141479](https://github.com/NousResearch/hermes-agent/pull/141479), [#142974](https://github.com/NousResearch/hermes-agent/pull/142974), [#144657](https://github.com/NousResearch/hermes-agent/pull/144657), [#141340](https://github.com/NousResearch/hermes-agent/pull/141340)) |
| [LangChain.js](https://github.com/langchain-ai/langchainjs) | Truncated streamed tool-call arguments are repaired into a valid-looking tool call instead of being dropped | [#11994](https://github.com/langchain-ai/langchainjs/pull/11994) open |
| [OpenAI Codex](https://github.com/openai/codex) | A streamed reply that turns into a repetition loop runs to the end — the CLI sends no output cap | [#55279](https://github.com/openai/codex/issues/55279) open |

Ecosystem fixes, merged or reported upstream:

| Project | What I found or fixed | Status |
|---|---|---|
| [SchemaStore](https://github.com/SchemaStore/schemastore) | `package.json` accepted an empty `version` string; two `cargo.json` constraints let through detailed dependencies cargo rejects | [#6421](https://github.com/SchemaStore/schemastore/pull/6421) merged · [#6394](https://github.com/SchemaStore/schemastore/issues/6394), [#6435](https://github.com/SchemaStore/schemastore/issues/6435) reported, fixed upstream |
| [scamgate](https://github.com/odaiameera/scamgate) | A long command line kept `extract()` busy for **about 40 seconds**; it now reads call-like text in one pass | [#21](https://github.com/odaiameera/scamgate/pull/21), [#22](https://github.com/odaiameera/scamgate/pull/22) merged |
| [nexus-memory](https://github.com/Neboy72/nexus-memory) | Reported **3 vulnerabilities** and wrote the fixes; credited in all three advisories — guardrail path rules that never matched Windows paths (high), cubic backtracking in the destructive-command patterns, and ReDoS in wikilink extraction; Windows import failure and symlink test guard still pending | [#10](https://github.com/Neboy72/nexus-memory/issues/10), [#11](https://github.com/Neboy72/nexus-memory/pull/11), [#12](https://github.com/Neboy72/nexus-memory/pull/12) open · advisories published 2026-10-28 |
| [Vercel](https://github.com/vercel/vercel) | `vercel.json` declares draft-04 but uses draft-06+ keywords, so strict validators reject it | [#17639](https://github.com/vercel/vercel/issues/17639) open |
| [Compose spec](https://github.com/compose-spec/compose-go) | No schema constraint requires `image`, `build` or `provider`, yet the loader rejects a service with none of them | [#933](https://github.com/compose-spec/compose-go/issues/933) open |

Projects listed in **4 curated awesome lists** through 6 merged pull requests.

## Original work

### Benchmarks — one fake endpoint, the same measurement for every client

- **[agent-double-exec-bench](https://github.com/simin-yuan/agent-double-exec-bench)** — seven agent frameworks against a server that fails on purpose: **one 429 becomes 27 identical requests**, and some frameworks decide whether to retry by reading the error text.
- **[agent-runaway-bench](https://github.com/simin-yuan/agent-runaway-bench)** — what clients do when the model loops mid-reply: **five clients across four frameworks return the entire loop as the final answer — 300,048 characters — and none of them stops.**
- **[agent-cutoff-bench](https://github.com/simin-yuan/agent-cutoff-bench)** — a tool call cut off mid-argument still runs: LangGraph writes **16 of 40 lines** to disk and reports success.
- **[retrieval-abstention-bench](https://github.com/simin-yuan/retrieval-abstention-bench)** — zero-dependency abstention test: the retriever declines **86.0%** of questions that are unanswerable by construction against **3.3%** of the answerable set, a **+82.7 point** gap (95% bootstrap CI +77.7 to +87.3).

### Hermes plugins (in the official catalog)

- **[hermes-degeneracy-guard](https://github.com/simin-yuan/hermes-degeneracy-guard)** — refuses degenerate, repetition-looped tool calls before they execute; **39 legitimate payloads** in the test corpus, and none of them prompts.
- **[hermes-provenance-gate](https://github.com/simin-yuan/hermes-provenance-gate)** — flags memory and skill writes that claim a user decision the user's own words do not back; **precision 1.000 (34/34), recall 0.694 (34/49)** on a hand-labelled set.
- **[hermes-skill-lineage](https://github.com/simin-yuan/hermes-skill-lineage)** — records where each skill came from, hash-chained, so a self-rewriting agent's skill set can be audited; **131 passed, 1 skipped** at v0.1.8.

### Verification tools

- **[greencheck](https://github.com/simin-yuan/greencheck)** — mutation testing for quality gates and metrics: the bundled demo gate catches most mutations and still lets **one bad configuration through** — the difference between "the check ran" and "the check worked".
- **[precheck](https://github.com/simin-yuan/precheck)** — freezes a check before an agent runs, hash-locked, then asks whether that check could have failed at all. Zero dependencies, no network.
- **[agent-pushgate](https://github.com/simin-yuan/agent-pushgate)** — leak, commit-message, push-range and ledger gates whose self-tests must go red when sabotaged; **68 self-test cases** across five gates.
- **[authority-chain](https://github.com/simin-yuan/authority-chain)** — hash-chained SQLite event log with base-revision tickets and countersigning for protected documents; **all 57 error codes** are exercised by a test.

### Audited, and self-auditing

- **[self-auditing-agent](https://github.com/simin-yuan/self-auditing-agent)** — a public audit log where every claim carries a rerunnable command; `gatecheck` ships inside it.

## Research

Constraint recovery in two-agent language-model loops — measured, not asserted:

- *Volume or Coupling? A Scale-Dependent Dissociation in Constraint Recovery of Language-Model Loops* — Zenodo, [10.5281/zenodo.21200851](https://doi.org/10.5281/zenodo.21200851)
  - Accepted versions at four NeurIPS 2026 workshops: New in ML, EvoRobust, Resource-Aware Agentic AI (RAAAI), and CL4FMAgents.
- *Recovery or Repetition? Feedback Policy and Verbatim Relay in a Two-Agent Language-Model Loop* — Research Square, [10.21203/rs.3.rs-11287076/v1](https://doi.org/10.21203/rs.3.rs-11287076/v1)

## How I work

- **Evidence before assertion.** Every claim in these repos ships with the command that reproduces it. When the evidence does not support the claim, the claim goes.
- **Gates that can say no.** A check that has never failed is a check that never ran, so I sabotage my own gates to prove they go red.
- **Publish failures.** The MISS, the regression and the broken version stay in the record. A repository that only shows wins is not evidence.
