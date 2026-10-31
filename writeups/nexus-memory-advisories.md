# Three security advisories in an agent memory layer: how they were found, measured and fixed

*Simin Yuan · November 2026*

An agent guardrail fails in two directions. It can block something it should allow, and the user finds out within a minute. Or it can allow something it should block, and nothing happens — because "allowed" looks exactly like "working". All three findings below are the second kind: each one is a check that returned the permissive answer, with confidence and without a warning.

I reported three issues to [nexus-memory](https://github.com/Neboy72/nexus-memory), a local shared memory layer for AI agents that also classifies terminal commands before an agent runs them. All three are fixed and published, each with regression tests:

| Advisory | What it is | Severity | Fixed in |
|---|---|---|---|
| [GHSA-2h94-xx3x-chrr](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-2h94-xx3x-chrr) | Guardrail path rules never match Windows paths; protected subpaths and parent deletes are allowed | High | 0.22.35 |
| [GHSA-m446-mvgq-w52p](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-m446-mvgq-w52p) | Cubic backtracking in the destructive-command patterns | Moderate | 0.22.34 |
| [GHSA-455p-688x-56r8](https://github.com/Neboy72/nexus-memory/security/advisories/GHSA-455p-688x-56r8) | Quadratic regex in wikilink extraction (CVSS 3.1 score 6.5) | Moderate | 0.22.32 |

If you run nexus-memory, upgrade to 0.22.35 or later.

## 1. The guardrail that answered "unprotected target" — GHSA-2h94 (High)

The guardrail lets you protect a path, for instance a folder under `<drive>:\data\protected`. Before a delete runs it asks two questions: is the target inside a protected path, and is a protected path inside the directory about to be deleted?

Both checks appended a literal `/` to the string they compared against. On Windows `os.path.normpath` returns backslashes, so the prefix could never match. With that rule in place, both of these came back as ALLOW:

```
rm -rf <drive>:\data\protected\file.txt
rm -rf <drive>:\data
```

The reason the engine gave was "Destructive action (delete) on unprotected target". That sentence is the whole bug: the check did not crash, log, or degrade — it concluded, confidently, that nothing was protected, and the delete was allowed to run.

**Scope, stated precisely,** because a severity rating is only useful if the reader can tell whether it applies to them: an exact match on the protected path itself was still blocked, and wildcard rules still worked. Only the two nested cases were lost. macOS and Linux were not affected — there the normalized path uses the same separator the check appends.

**Why swapping the separator is not enough.** The obvious patch is to append `os.sep` instead of `/`. It fixes the common case and still fails at a drive root: normalizing a bare drive root leaves a trailing separator, and appending another one produces `<drive>:\\`, which never prefixes a child path such as `<drive>:\data\x.txt`. A rule protecting a whole drive stays silently open. The fix that shipped compares path components rather than string prefixes, which covers the drive root, and it adds a hook so the test suite can run the matcher under both Windows and POSIX path rules on any machine — that is what makes the regression test meaningful on a Linux CI box. The landed commit (`545bb14`) is authored by the maintainer's account; the advisory and the changelog credit the fix to me.

## 2. A pre-execution check that can be made to stall — GHSA-m446 (Moderate)

Several destructive-command patterns had the shape `A.*B`; the worst was `\bgit\b.*clean.*-[fd]`. When a command repeats the first part and never reaches the last, the engine retries from every occurrence of `A` and backtracks through every candidate, so cost grows with the cube of length. In the run recorded in the advisory, a command made of repeated `git clean ` took **0.44 s at 8,000 characters and 3.45 s at 16,000** — doubling the input multiplied the time by about eight.

The same shape appeared in the pip, npm and brew install patterns, in `dd ... of`, in `find ... -delete`, and in the `DELETE ... collection` and `drop ... collection` rules.

This is a pre-execution gate: `classify_action()` runs before a terminal command executes in the Hermes memory plugin, the MCP server and the quick-check path, and nothing caps the length of a command being classified. A command shaped by a web page or by a tool result can therefore hold the check for minutes — the guardrail becomes the delay.

The fix writes the eight patterns as `(?m)^(?>.*?A).*?B`. The atomic lazy group commits to the first `A` on each line, which for fixed tokens is always the correct choice, so the scan is linear and the verdict does not change. The 16,000-character command went from 3.45 s to **0.1 ms**. To show the verdicts really are unchanged, the regression file keeps the old patterns as an oracle and compares them against the new ones over generated commands — 480,000 of them, no difference.

## 3. Memory text that can stall the agent — GHSA-455p (Moderate, CVSS 6.5)

nexus-memory extracts `[[wikilinks]]` from stored facts with `\[\[([^\]]+)\]\]`. The negated class cannot cross a `]`, so every `[[` without a closing pair rescans the rest of the string: quadratic in the length of the fact. In the run recorded in the advisory, a fact of 32,000 opening brackets followed by one closing bracket took **1.60 s at 32,001 characters, 6.40 s at 64,001 and 15.73 s at 100,001**. Discovery is single-threaded, so the agent blocks for the whole run.

Facts can carry text from web pages, tool results or prompt injection, and the length of a fact is not capped — which is why a formatting pattern is worth a security advisory. The fix replaces the regex with a single left-to-right scan that produces identical output, and ships an equivalence suite (`tests/test_classifier_wikilink_linear.py`) that compares the scan against the old pattern on generated text.

## How the work was done

Three rules carried all three reports.

**Ask the pattern for its worst case, then time it.** Not "this looks like a ReDoS", but a concrete input and a measured curve: 32,001 → 64,001 → 100,001 characters, 1.60 s → 6.40 s → 15.73 s. A curve is reproducible by whoever reads it; an adjective is not. When the curve was steep, the report carried it; when a shape looked risky and the numbers were flat, that shape stayed out of the report.

**Report privately, with the patch and the test.** All three went to the maintainer before anything was public, with the patch in the same message, so root cause and fix became public together with the fixed release, never before it.

**The regression test has to be red on the old code.** Before a patch leaves my machine, its new tests run against the unpatched tree and must fail there, then against the patched tree and must pass. A test that passes on both proves nothing about the fix — it proves the test is not looking. For cross-platform defects there is a second gate: the matcher takes the path module as a parameter, so a POSIX CI runner exercises the Windows code path that would otherwise only reproduce on a developer's laptop.

## Where each claim can be checked

| Claim | Where it comes from |
|---|---|
| Severity, affected range, fixed version, credit | The three advisories linked above — public, and edited only by the maintainer |
| The credit sentence | `CHANGELOG.md` on `main`: "Reported and fixed by Simin Yuan (@simin-yuan)", once per fix — in the 0.22.32, 0.22.34 and 0.22.35 entries |
| The 0.22.34 rewrite | commit `0a713bb` (authored under @simin-yuan), carried into the release by `f2f21f4`; `tests/test_guardrails_linear.py` |
| The 0.22.35 comparison fix | commit `545bb14`, `src/nexus_memory/guardrails.py`, `tests/test_guardrails_paths_crossplatform.py` |
| The 0.22.32 rewrite | `nexus/discovery/classifier.py` and `tests/test_classifier_wikilink_linear.py` on tag `v0.22.32` |
| Every timing number above | Quoted verbatim from the advisory text; each advisory names the run it came from |

## Who did what

I reported all three and wrote the fixes, with regression tests for m446 and 2h94. The 0.22.34 fix is committed under my name (`0a713bb`). For 0.22.32 and 0.22.35 the landed commits are the maintainer's; the changelog and the advisories credit the fixes to me, and the 0.22.32 entry notes that the shipped scan comes from the report. The finder field on all three reads `simin-yuan`, and all three advisory descriptions state that I reported the issue and wrote the fix.

The maintainer (Neboy72) reviewed and released all three fixes, published all three advisories on the same day, and wrote the credit into each one himself.

## The general point

A guardrail that blocks too much gets fixed, because someone complains the same day. A guardrail that allows too much stays wrong until someone tests the allow path on purpose — and reads the reason string, not just the verdict. If you build or depend on agent guardrails:

- test the cases where the answer should be *no*, on every platform you claim to support;
- give every pattern a worst-case input and a time, not a suspicion;
- treat a test that has never failed as a test that has never run.

---

## Cite this

> Simin Yuan. *Three security advisories in an agent memory layer: how they were found, measured and fixed.* November 2026. github.com/simin-yuan/simin-yuan/blob/main/writeups/nexus-memory-advisories.md

```bibtex
@misc{yuan2026nexusadvisories,
  author       = {Yuan, Simin},
  title        = {Three security advisories in an agent memory layer:
                  how they were found, measured and fixed},
  year         = {2026},
  month        = {nov},
  howpublished = {\url{https://github.com/simin-yuan/simin-yuan/blob/main/writeups/nexus-memory-advisories.md}},
  note         = {Advisories GHSA-2h94-xx3x-chrr, GHSA-m446-mvgq-w52p, GHSA-455p-688x-56r8}
}
```

---

**Authorship.** I chose what to audit, judged which findings were worth a report, and decided each fix and the test that proves it. AI agents did much of the legwork under my direction — running the timing loops, drafting patches and tests, diffing old and new scans — and I reviewed everything before it left the machine. Nothing here rests on a tool's word: every number is quoted from a public advisory, and every claim points to the commit, the test file or the line you can check it against.

*Simin Yuan · [github.com/simin-yuan](https://github.com/simin-yuan) · Light Emerge Co., Ltd. — I audit agent systems for reliability and security: guardrails, retries, stream handling, memory extraction. [lightemerge.com](https://lightemerge.com)*

What I check in an agent system:

- **Pre-execution gates:** a worst-case input and a timing curve for every pattern that runs before a command or a write.
- **The allow path:** the cases where the answer should be *no*, with the reason string read, not just the verdict.
- **Platform rules:** path, separator and encoding behaviour on every platform the system claims to support.
