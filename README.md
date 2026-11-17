# Simin Yuan

**Founder, Light Emerge** · Independent researcher, agent reliability

I find where AI agents fail — runaway loops, duplicate execution, tool calls cut off
mid-argument, memory that records a decision nobody made — measure it reproducibly,
and fix it upstream.

[lightemerge.com](https://lightemerge.com) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ) · [ORCID](https://orcid.org/0009-0002-6843-3390) · [LinkedIn](https://www.linkedin.com/in/simin-yuan-2845a03bb) · [X @SiminYuanAI](https://x.com/SiminYuanAI)

Open to collaboration and relevant roles.

## Highlights

- **33 pull requests merged in 16 upstream projects** — Microsoft agent-framework, NVIDIA NeMo (Agent Toolkit, Relay), deepset Haystack, the OWASP GenAI Security Project, ModelScope, SchemaStore, NousResearch.
- **Six accepted versions of three manuscripts at NeurIPS 2026 workshops** — New in ML, EvoRobust, Resource-Aware Agentic AI, CL4FMAgents, AI and the Self, SocialAgent.
- **Three plugins** accepted into the official Hermes Agent plugin catalog, and **three credited security advisories** in an agent memory layer.

## Merged upstream contributions

Merged only.

| Project | PR | What changed |
|---|---|---|
| **microsoft/agent-framework** | [#10202](https://github.com/microsoft/agent-framework/pull/10202) | An over-long Windows path is reported as `ENAMETOOLONG`, not as a missing file |
| | [#10200](https://github.com/microsoft/agent-framework/pull/10200) | Atomic session-snapshot replacement retries on a transient `PermissionError` |
| **NVIDIA/NeMo-Agent-Toolkit** | [#2551](https://github.com/NVIDIA/NeMo-Agent-Toolkit/pull/2551) | `tool_calling_agent`: a tool call cut off mid-argument no longer runs |
| | [#2553](https://github.com/NVIDIA/NeMo-Agent-Toolkit/pull/2553) | Same guard in `react_agent` with native tool calling |
| **NVIDIA/NeMo-Relay** | [#1675](https://github.com/NVIDIA/NeMo-Relay/pull/1675) | Anthropic thinking and signature deltas are accumulated, not dropped |
| **deepset-ai/haystack** | [#13583](https://github.com/deepset-ai/haystack/pull/13583) | Reject `NaN` scores in `MetaFieldRanker` linear-score mode |
| **OWASP GenAI · agent-control-standard** | [#526](https://github.com/GenAI-Security-Project/agent-control-standard/pull/526) | The example Guardian refuses a repeated nonce, as spec §10.3 asks |
| | [#525](https://github.com/GenAI-Security-Project/agent-control-standard/pull/525) | The example Guardian starts on Windows |
| **modelscope/evalscope** | [#2075](https://github.com/modelscope/evalscope/pull/2075) | Multi-select: read bracketed labels that run together |
| | [#2064](https://github.com/modelscope/evalscope/pull/2064) | Reasoning-safe request defaults on `api.openai.com` |
| | [#2035](https://github.com/modelscope/evalscope/pull/2035) | One shared last-JSON-object extractor |
| | [#2031](https://github.com/modelscope/evalscope/pull/2031) | `longpi_bench`: keep the last parseable JSON object |
| | [#2030](https://github.com/modelscope/evalscope/pull/2030) | `zebralogicbench`: braces inside strings stay out of the object scan |
| | [#2022](https://github.com/modelscope/evalscope/pull/2022) | `agentx`: replay local Weka mirrors through the native loader |
| **NousResearch/hermes-agent** | [#141341](https://github.com/NousResearch/hermes-agent/pull/141341) · [#141935](https://github.com/NousResearch/hermes-agent/pull/141935) · [#142381](https://github.com/NousResearch/hermes-agent/pull/142381) | `degeneracy-guard`, `provenance-gate` and `skill-lineage` enter the official plugin catalog |
| | [#144339](https://github.com/NousResearch/hermes-agent/pull/144339) · [#154393](https://github.com/NousResearch/hermes-agent/pull/154393) | Re-pin `provenance-gate`; bump `skill-lineage` to 0.2.0 |
| **SchemaStore** | [#6421](https://github.com/SchemaStore/schemastore/pull/6421) | `package.json`: reject an empty `version` string |
| **kiwicom/pytest-recording** | [#213](https://github.com/kiwicom/pytest-recording/pull/213) | Allow `socketpair` under the network-blocking guard |
| **mindflayer/python-mocket** | [#334](https://github.com/mindflayer/python-mocket/pull/334) | Fix `socketpair()` where `_socket.socketpair` is absent |

## Security advisories

Three issues found and fixed in [nexus-memory](https://github.com/Neboy72/nexus-memory), published with regression tests and credited in the advisory text.

| Advisory | What was wrong | Severity | Fixed in |
|---|---|---|---|
| [GHSA-2h94-xx3x-chrr](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-2h94-xx3x-chrr) | Guardrail path rules never matched Windows paths, so protected subpaths and parent deletes were allowed | High | 0.22.35 |
| [GHSA-m446-mvgq-w52p](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-m446-mvgq-w52p) | Cubic backtracking in the destructive-command patterns of the pre-execution check | Moderate | 0.22.34 |
| [GHSA-455p-688x-56r8](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-455p-688x-56r8) | Quadratic regex in wikilink extraction (CVSS 3.1: 6.5) | Moderate | 0.22.32 |

**Write-up:** [Three security advisories in an agent memory layer](writeups/nexus-memory-advisories.md) — how each was found, the measured curve, the fix, and where every number can be checked.

## Research & benchmarks

One fake endpoint, the same measurement for every client. No API keys, no spend; anyone can rerun.

- **[agent-faultbench](https://github.com/simin-yuan/agent-faultbench)** — an agent's self-report says it did A, the write on disk says it did B. A tool call cut off inside line 17 still executed: **866 of 2,120 bytes** written, nothing raised. The rows reproducing that defect are the ones fixed upstream in NeMo Agent Toolkit ([#2550](https://github.com/NVIDIA/NeMo-Agent-Toolkit/issues/2550), [#2552](https://github.com/NVIDIA/NeMo-Agent-Toolkit/issues/2552) → #2551, #2553 above). Runs archived: [10.5281/zenodo.24143645](https://doi.org/10.5281/zenodo.24143645).
- **[agent-double-exec-bench](https://github.com/simin-yuan/agent-double-exec-bench)** (archived; now `suites/double-exec`) — seven frameworks against a server that fails on purpose: one 429 becomes **27 identical requests** in CrewAI, 9 in smolagents, 3 in most others; only CrewAI re-runs the tool, three times.
- **[agent-runaway-bench](https://github.com/simin-yuan/agent-runaway-bench)** (archived; now `suites/runaway`) — when the model loops inside one reply, **six clients across five frameworks return the whole 300,048-character loop as the answer**; Hermes Agent's own repetition detector stopped at 16,168 characters.
- **[agent-cutoff-bench](https://github.com/simin-yuan/agent-cutoff-bench)** (archived; now `suites/cutoff`) — a tool call whose arguments were cut off still runs: LangGraph writes **16 of 40 lines** and reports success.
- **[retrieval-abstention-bench](https://github.com/simin-yuan/retrieval-abstention-bench)** — zero dependency, no network: the retriever declines **86.0%** of questions that are unanswerable by construction against **3.3%** of the answerable set, a **+82.7 point** gap (95% bootstrap CI +77.7 to +87.3).
- **[greencheck](https://github.com/simin-yuan/greencheck)** — mutation testing for validators, metrics and quality gates: a check is not evidence until it has been observed to react to inputs known to differ.

Raw records for the three suites: [10.5281/zenodo.23808696](https://doi.org/10.5281/zenodo.23808696) — 721 files, CC-BY-4.0.

### Hermes plugins (official catalog)

- **[hermes-degeneracy-guard](https://github.com/simin-yuan/hermes-degeneracy-guard)** — refuses degenerate, repetition-looped tool calls before they execute; 39 legitimate payloads in the test corpus, none of them prompts.
- **[hermes-provenance-gate](https://github.com/simin-yuan/hermes-provenance-gate)** — flags memory and skill writes that claim a user decision the user's own words do not back; precision 1.000 (34/34), recall 0.694 (34/49).
- **[hermes-skill-lineage](https://github.com/simin-yuan/hermes-skill-lineage)** — records where each skill came from, hash-chained, so a self-rewriting agent's skill set can be audited; 131 passed, 1 skipped.

Verification tooling also lives in [precheck](https://github.com/simin-yuan/precheck) (hash-locked checks that ask whether they could have failed), [agent-pushgate](https://github.com/simin-yuan/agent-pushgate) (leak and push-range gates whose self-tests must go red when sabotaged) and [authority-chain](https://github.com/simin-yuan/authority-chain) (hash-chained event log with base-revision tickets).

## Papers

Six accepted versions of three manuscripts, NeurIPS 2026 workshops.

**One model, four workshops — constraint recovery in language-model loops**

- **Volume or Coupling? A Scale-Dependent Dissociation in Constraint Recovery of Language-Model Loops** — New in ML, EvoRobust (4-page version), RAAAI (as *Volume or Coupling: What Constraint Recovery Costs*) and CL4FMAgents. At 1.5B the coupled loop beats a single model 43% to 17%, and a volume-matched single model reproduces the advantage (37%, p = .774): what looks like coupling is a context-volume effect at that scale, and across scale the dissociation reverses. [10.5281/zenodo.21200851](https://doi.org/10.5281/zenodo.21200851)

**Two single-workshop papers — what an agent's history is made of, and what it can be said to own**

- **Stake and Steerability: An Ontological Constraint on Artificial Agency and Subjectivity** — AI and the Self. Asks where a persistent agent's account of what it knows about its user, and about itself, actually comes from, and what of that account would survive a copy. [NeurIPS programme](https://neurips.cc/virtual/2026/175389) · [OpenReview](https://openreview.net/forum?id=Dz9oM89qgu)
- **History Is Not Memory: Stake, continual learning, and persistent AI agents** — SocialAgent. An agent learning over 42 sessions with five simulated users, under a preregistration and two independent audits: halting, resuming or copying it left no residue down to the last stored bit, while copying the memory alone lost most of what it knew. [10.5281/zenodo.23170703](https://doi.org/10.5281/zenodo.23170703)

Preprint, not a workshop paper: *Recovery or Repetition? Feedback Policy and Verbatim Relay in a Two-Agent Language-Model Loop* — [10.21203/rs.3.rs-11287076/v1](https://doi.org/10.21203/rs.3.rs-11287076/v1).

## How I work

- **Evidence before assertion.** Every claim in these repos ships with the command that reproduces it. When the evidence does not support the claim, the claim goes.
- **Gates that can say no.** A check that has never failed is a check that never ran, so I sabotage my own gates to prove they go red.
- **Publish failures.** The miss, the regression and the broken version stay in the record. A repository that only shows wins is not evidence.

## Contact

[X @SiminYuanAI](https://x.com/SiminYuanAI) · [lightemerge.com](https://lightemerge.com)
