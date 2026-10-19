# Reproducible evidence, falsifiable claims

**Independent researcher working on AI agent verification.** I build small tools that check claims — mine and other people's — and publish the runs where the answer came back no.

[GitHub](https://github.com/simin-yuan) · [ORCID 0009-0002-6843-3390](https://orcid.org/0009-0002-6843-3390) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ)

---

## Seven public works

### 1. [`precheck`](https://github.com/simin-yuan/precheck) — an agent cannot pass a check it was not allowed to write

The acceptance check is frozen before the work starts and hash-locked, so rewriting it afterwards voids every verdict issued after that point. Once a check passes, the tool breaks the artifact the check just approved and runs the check again: a check that still passes on broken input was never evidence for anything.

- `python demo.py` in a checkout, run 2026-10-10 — with `replicas: 3` changed to `0` the check still exits `0`, and `audit` reports `2 check/mutation pair(s) survived`
- 19 unit tests, zero dependencies, standard library only, no network; CI green on Python 3.8 / 3.11 / 3.13 across Linux, Windows and macOS (9/9)
- On PyPI: `pip install precheck` (v0.1.2)
- Ships a GitHub Action

### 2. [`greencheck`](https://github.com/simin-yuan/greencheck) — mutation testing aimed at quality gates

It edits your configs and schemas in small ways — drop a line, blank a value, set a number to zero — and reports the ones your gate still accepts.

- `python -m greencheck.cli mutate --gate "..." --target examples/mutate-demo/input` in a checkout, run 2026-10-10 — 12 mutants, 11 caught, 1 escaped (`blank-value:config.json:4:"replicas":`)
- 72 tests, zero dependencies, standard library only; CI green on Python 3.9 / 3.11 / 3.13 across Linux, Windows and macOS (9/9)
- On PyPI: `pip install greencheck` (v0.3.1)
- Its own output calls the result "questions, not findings": an escaped mutant may be a legal input, and the tool cannot tell you which without someone who knows what the gate is for

### 3. [`self-auditing-agent`](https://github.com/simin-yuan/self-auditing-agent) — an AI that audits itself in public

A published record of one run: every conclusion, the command that produced it, the bugs found in the audit tooling along the way, and the findings that were withdrawn. A stranger can re-run the core result.

- Volume 1 — a 74-minute forensic audit of an unfamiliar third-party system, a real gap found in it (the vulnerable pattern is reproduced offline in the repo), 4 bugs of its own, and one false discovery it caught itself
- The core finding reproduces with one command: `python repro/verify_sql_gap.py`
- The same repository turns its own gates into mutation targets and publishes which mutants escape

### 4. [`agent-pushgate`](https://github.com/simin-yuan/agent-pushgate) — privacy gates on the manual push path

Automated export pipelines usually have a leak gate; a hand-typed `git commit && git push` usually has none, and that is where private material tends to escape. Six checks run on the `pre-push` hook — the working tree, the commit messages, the exact object range being sent, the hook installation itself, landing, and ledger discipline. Any non-zero return, including "cannot decide", refuses the push.

- Local commits are untouched: stopping is reversible, publishing is not
- 6 self-test and 3 falsification scripts; every gate is fed input it must reject. **CI green on Linux and Windows × Python 3.9 and 3.13 (4/4 cells)**
- `git push --no-verify` bypasses it entirely, so one self-test measures that hole rather than pretending it is not there
- Zero third-party dependencies, standard library only; the deny-list lives outside the repository

### 5. [`retrieval-abstention-bench`](https://github.com/simin-yuan/retrieval-abstention-bench) — can a retrieval layer tell when the answer is not in the corpus?

Four question sets over four public-domain novels: one answerable, three unanswerable by construction. The false ones are built by swapping an entity anchor for a real entity that sits far away in the text, and whether they are genuinely unanswerable is checked numerically instead of asserted — a hand-written negative set only covers the cases its author imagined.

- `python bench/cli.py verify` in a checkout, run 2026-10-11 — 14 self-checks and 7 falsification cases, 33 s, exit 0; an oracle retriever drives the metric to 1.000 and a retriever that returns nothing drives it to 0.000
- Headline cell, a zero-dependency BM25 retriever: declines 3.3% of the answerable questions and 86.0% of the unanswerable ones — 82.7 points apart, 95% CI 77.7 to 87.3
- No LLM, no network, no API key, standard library only; the exam is frozen with a sha256, and `verify` fails if a recorded threshold no longer matches the code
- One criterion is easier than it should be on the cross-book tier (AUC 0.634 against 0.520 on the same-book tier), because the swapped anchor occurs nowhere in the target book at all; the headline rests on the criterion that has no such shortcut
- CI green on Python 3.9 and 3.12

### 6. [`hermes-degeneracy-guard`](https://github.com/simin-yuan/hermes-degeneracy-guard) — a looping model cannot write its loop into a file

Hermes already truncates runaway text, but it deliberately lets a provider-confirmed tool call through even when its arguments are a repetition loop. This plugin sits at `pre_tool_call` and refuses those calls before the tool runs.

- `hermes plugins doctor degeneracy-guard --ci`, run 2026-10-19 — `OK`, `5 hook(s)`; `hermes plugins validate` — `Validation passed`, security scan `safe`
- 21 tests, including one that drives a degenerate call through Hermes' real plugin manager and checks it is blocked
- CI runs against a pinned Hermes commit on Python 3.11–3.14, and weekly against Hermes main
- Standard library only; no network, no shell, no telemetry

### 7. [`authority-chain`](https://github.com/simin-yuan/authority-chain) — every rewrite an agent makes to its own rules is auditable

A hash-chained SQLite event log. A write must name the revision it read, or it is refused; a stale write is queued, not lost; protected documents need a human countersign bound to the exact content.

- README quick start, run verbatim in an empty environment in CI — ends with `chain_status INTACT`
- 25 tests; CI green on Python 3.9 / 3.11 / 3.13
- Every refusal has a named code, documented in `docs/errors.md`

---

## What I work on

**An AI system earns trust to the extent that it lets you check where it went wrong.**

Three rules follow:

| Rule | In practice |
|---|---|
| Evidence before assertion | Every claim carries the command that produced it and its output |
| A gate must be able to say no | Gates are fed inputs that should fail; a check that cannot return a negative is not a check |
| Record the failures | Bugs, false alarms and withdrawn findings are published; a success-only showcase is not credible |

---

## Publications

- *Volume or Coupling? A Scale-Dependent Dissociation in Constraint Recovery of Language-Model Loops.* Zenodo, 2026-07-05. DOI [10.5281/zenodo.21200851](https://doi.org/10.5281/zenodo.21200851)
- *Recovery or Repetition? Feedback Policy and Verbatim Relay in a Two-Agent Language-Model Loop.* Research Square, 2026-10-08. DOI [10.21203/rs.3.rs-11287076/v1](https://doi.org/10.21203/rs.3.rs-11287076/v1)
- *Can One Agent Restore Another? Multi-Agent Verification Through Independent Constraint Sources.* Research Square, 2026-08-13. DOI [10.21203/rs.3.rs-10651733/v1](https://doi.org/10.21203/rs.3.rs-10651733/v1)

[ORCID](https://orcid.org/0009-0002-6843-3390) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ)

---

## 中文

**独立研究者。做可被验证的事，并公开我被验证错了的部分。**

[GitHub](https://github.com/simin-yuan) · [ORCID 0009-0002-6843-3390](https://orcid.org/0009-0002-6843-3390) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ)

### 五个公开作品

#### 1. [`precheck`](https://github.com/simin-yuan/precheck) — 让 agent 用「它无权编写」的检查自证

验收检查在动工前就冻结并锁进哈希链，事后改一个字，那之后的全部结论作废。检查通过后，它改坏刚被批准的产物再跑一遍：在坏输入上照样通过的检查，从来就不是证据。

- 仓内取一份代码跑 `python demo.py`（2026-10-10 实测）——`replicas: 3` 改成 `0`，检查照样 `exit 0`，`audit` 报 `2 check/mutation pair(s) survived`
- 19 个单测、零依赖、纯标准库、不联网；CI 在 Python 3.8 / 3.11 / 3.13 × Linux / Windows / macOS 全绿（9/9）
- 已上 PyPI：`pip install precheck`（v0.1.2）
- 自带 GitHub Action

#### 2. [`greencheck`](https://github.com/simin-yuan/greencheck) — 专查质量门禁的变异测试工具

它在你的配置和 schema 上做小改动——删一行、把值清空、把数字改成 0——然后报告哪些门禁照样收下了。

- 仓内取一份代码实跑（2026-10-10）：12 个变异，11 个被拦，1 个逃过（`blank-value:config.json:4:"replicas":`）
- 72 个测试、零依赖、纯标准库；CI 在 Python 3.9 / 3.11 / 3.13 × Linux / Windows / macOS 全绿（9/9）
- 已上 PyPI：`pip install greencheck`（v0.3.1）
- 它自己的输出写着「这些是问题、不是结论」：逃过的变异可能是合法输入，没有懂这条门禁要干什么的人，工具分不出来

#### 3. [`self-auditing-agent`](https://github.com/simin-yuan/self-auditing-agent) — 一个会自我审计的 AI

一份公开的运行档案：每个结论、产出它的那条命令、审计工具自己出的 bug、以及被撤回的发现。陌生的人可以复跑核心结果。

- 第一卷——74 分钟取证审计一个陌生第三方系统；在它里面找到一个真缺口（漏洞模式已在仓库里离线复现）；4 个自己的 bug；1 个假发现是自己抓出来的
- 核心发现一条命令可复现：`python repro/verify_sql_gap.py`
- 同一个仓库把自己门禁当变异靶子，公开哪些变异逃了过去

#### 4. [`agent-pushgate`](https://github.com/simin-yuan/agent-pushgate) — 手工推送前的隐私闸

自动导出的那条路径通常有闸，手打 `git commit && git push` 通常没有，私密内容多半从这儿漏出去。六个检查串在 `pre-push` 钩子上——工作树、提交信息、这次要送的确切对象范围、钩子有没有真装上、落地、台账纪律。任何一个返回非 0（含「判不了」）就拒绝这次推送。

- 本地提交不受影响：**停下来是可逆的，公开不可逆**
- 6 个自证 + 3 个证伪脚本；每个闸都拿「必须被拒的输入」喂过。**CI 在 Linux 与 Windows × Python 3.9 / 3.13 四格全绿**
- `git push --no-verify` 能整个绕过它——所以有一条自证把这个洞实测出来，不假装它不存在
- 零第三方依赖、纯标准库；禁止名单不进仓

#### 5. [`retrieval-abstention-bench`](https://github.com/simin-yuan/retrieval-abstention-bench) — 检索层知不知道「库里根本没有答案」

四部公有领域小说、四套题：一套可答，三套**构造性不可答**。假题的做法是把实体锚点换成一个真实但离得很远的实体，它到底可不可答由距离规则**算出来**，不是拍脑袋断言——手写的负样本只能覆盖作者想得到的那几个。

- 仓内取一份代码跑 `python bench/cli.py verify`（2026-10-11 实测）——14 项自检 + 7 条证伪，33 秒，exit 0；oracle 检索器把指标顶到 1.000，空检索器把它压到 0.000
- 头条格（零依赖 BM25）：可答题弃答 3.3%，不可答题弃答 86.0%——相差 82.7 个百分点，95% CI 77.7 ~ 87.3
- 不用 LLM、不联网、不要 API key、纯标准库；考试封了 sha256，`verify` 发现记录的阈值与代码不符就报红
- 有一条判据在跨书档上比它该有的更容易（AUC 0.634，同书档 0.520），因为换过去的锚点在目标书里完全不出现；头条压在没这条捷径的判据上
- CI 在 Python 3.9 / 3.12 全绿

### 我在做什么

**一个 AI 值得多少信任，取决于它在多大程度上让人查它错在哪。**

由此三条规则：

| 规则 | 落地形态 |
|---|---|
| 证据先于断言 | 每条结论都带着产出它的命令和输出 |
| 门禁必须能说「不」 | 门禁只用**必须失败的输入**去喂；不能输出否定的判据不算判据 |
| 记录失败 | bug、误报、被撤回的发现全部公开；只放成功路径的展示不可信 |

### 论文

- *Volume or Coupling? A Scale-Dependent Dissociation in Constraint Recovery of Language-Model Loops.* Zenodo，2026-07-05。DOI [10.5281/zenodo.21200851](https://doi.org/10.5281/zenodo.21200851)
- *Recovery or Repetition? Feedback Policy and Verbatim Relay in a Two-Agent Language-Model Loop.* Research Square，2026-10-08。DOI [10.21203/rs.3.rs-11287076/v1](https://doi.org/10.21203/rs.3.rs-11287076/v1)
- *Can One Agent Restore Another? Multi-Agent Verification Through Independent Constraint Sources.* Research Square，2026-08-13。DOI [10.21203/rs.3.rs-10651733/v1](https://doi.org/10.21203/rs.3.rs-10651733/v1)

[ORCID](https://orcid.org/0009-0002-6843-3390) · [Google Scholar](https://scholar.google.com/citations?user=Ae_nfUYAAAAJ)

---

— Simin (Simon) Yuan
