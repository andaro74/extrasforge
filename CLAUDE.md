# CLAUDE.md — extrasforge

BDA describes the media; extrasforge shows that a description reaches
the knowledge base only by passing a rule one named person owns, and
that the agent cannot state a machine-written description as fact.

SPEC/00-overview.md is the authority. If this file and SPEC/00 disagree,
SPEC/00 wins and this file gets a PR.

## How a session starts

1. Read `milestones/README.md` (the ledger) and the open milestone's
   `milestones/MNN/README.md` (its row plus open/close detail).
2. State back, in two sentences: the claim, and the commit or input that
   makes it false. If you cannot, stop and say so. Do not touch code.
3. Say which PR of the milestone this session is (1–4) and what that PR
   is allowed to contain (below).
4. One milestone per session. A session that drifts to the next milestone
   ends.

## The PR shape (cap four, no spare)

- **PR 1 — plant.** SPEC/NN, `milestones/MNN/feasibility.md` (with the
  `product-spec-reviewer` report pasted in), ledger row on open in
  `milestones/README.md` and `milestones/MNN/README.md`, explainer draft
  (`docs/milestones/MNN.md`, "What happened" empty), the seeded false
  state committed. Nothing that makes it pass.
- **PR 2 — measure.** The gate that reads the plant. The plant must go
  RED here. This PR is the measurement; later PRs do not decide the row.
- **PR 3 — repair.** Whatever the cold review of PR 2 found. If it found
  nothing, PR 3 is skipped and the milestone closes at PR 3 as the close
  PR.
- **PR 4 — close.** Ledger "measured" cell, explainer "What happened",
  video README entry, attestations, `git tag mNN`. M00 only: the three
  skills, written from by-hand PRs 1–3.
- A milestone may close in three PRs; never in five. A fifth PR is a RED
  close with the finding as the result. Do not propose a cap raise; write
  the finding.

A PR lands on `main` as a merge commit, never a rebase or a squash
(ADR-0004 amendment 1): envelopes are keyed to the commit they measured,
and `github-actions[bot]` authorship under `evals/history/` is part of
what makes them evidence.

Every PR ends with a ruling file at `milestones/MNN/rulings/<slug>.md`
(front matter: `ruling`, `seat`, `authorises`, `evidence`, `pr`), not
with a merge. `cold-review-ruling` enforces this from M00 PR 2 and blocks
the merge until the ruling is on `main`. A PR touching a milestone's
build paths cites `SPEC/00-overview.md#8-MNN` as its ruling. You write
the diff; you do not merge.

## Never (these are what turned beaconpave's milestones RED)

- Never write a claim whose false state is not already in the repo.
- Never build the machinery that reads a claim in the last PR.
- Never let the corpus that judges an answer also supply the answer.
  (`validate` checks golden/retrieval overlap; do not route around it.)
- Never edit `src/baseline/` after tag `m00`. It is the control.
- Never write an envelope by hand or from a runner. Only
  `src/verdict/build.py` writes envelopes; only `src/verdict/gate.py`
  reads them; a test proves they can disagree.
- Never touch `evals/goldens/`, `thresholds.yaml`, `rules/`,
  `data/titles/`, `data/plants/` or the guardrail/judge/model ids in
  `manifest.yaml`
  without naming the seat that owns the path and the ruling that
  authorises it. Propose the diff; the seat's PR carries it.
- Never rename a golden id. Retire it.
- Never treat a local run (`evals/local/`) as evidence.
- Never hand-edit `docs/milestones/README.md`; `make ledger-plain`
  writes it from the ledger.
- Never write a word that says a control holds (agentkeel's CLAUDE.md
  names the three) in prose about a control that has not fired on its
  seeded case.
- Never summarise the project's state from its own prose. Read the
  ledger and the envelopes; the prose has been wrong before.
- Never add a subagent before the milestone that first needs it (R8).
- Never add a judge check for something a rule in the tree already
  catches (P12).
- Never index a field without provenance and status (P13).
- Never put a provider model id anywhere but
  `infra/gateway/inference-targets.yaml`; an agent manifest carries a
  `model_alias` (R13).
- Never put real film titles, real contracts or real studio workflow
  detail anywhere. The slate is fictional; footage is CC-BY stand-in
  with title cards trimmed (R12).

## Seats and paths (SPEC/00 §5)

| Path | Seat | Gate |
|---|---|---|
| `SPEC/**`, `milestones/**`, `CLAUDE.md`, `.claude/skills/**`, `docs/**`, `README.md`, `LICENSE`, `NOTICE` | Product | `ruling-cited` |
| `rules/**` (the rulebook, one YAML per rule with tests), `agents/*/rules/**`, guardrail id/version in manifest; in `agents/ingest/blueprints/*.json`, enum and `unknown` handling | Rule Owner | `ruling-cited`, `two-key` on relaxation |
| `evals/goldens/**`, `data/**` (slate, title metadata, cast and character lists, spoiler terms, rights table, planted-error register), reviewer decisions that change a golden, corpus admission; `data/plants/register.yaml`; `data/decisions/` (written by the review CLI, never by hand); in `agents/ingest/blueprints/*.json`, which fields exist and which are `generated` | Data Owner | `ruling-cited`, `two-key` on retire |
| `tools/**`, `agents/*/tools/**` (three MCP servers' schemas), `may_call`, `may_be_called_by` | Tool Owner | `ruling-cited`, computed semver |
| `thresholds.yaml` (triage bands per field, calibration method, catch-rate bar, cost cap, per-user budget), judge rubric, judge model id, agent model id + version + region, BDA project version pinned in manifest, `infra/gateway/inference-targets.yaml` (alias → provider model mapping); in `agents/ingest/blueprints/*.json`, the project id and version | Threshold Owner | `two-key` on any downward move; `two-key` on any change to `infra/gateway/inference-targets.yaml` |
| `.github/workflows/**`, `infra/**` (but for `infra/gateway/inference-targets.yaml`), KMS key policy, cosign identity, identity scoping (Cognito groups → title prefixes; KB metadata filter), gateway interceptors (`src/gateway/interceptors/**`), seats → groups in a manifest | Security | `ruling-cited`, `security-reviewer` |
| `src/**` (but for `src/gateway/interceptors/**`), `scripts/**`, `tests/**` (`tests/fixtures/bda/**` holds raw BDA outputs exactly as returned), `Makefile`, root config, `agents/<name>/**` but for the fields other seats own, `evals/history/**` (CI-written only), `evals/local/**` (gitignored, no gate) | Engineering | `cold-review-ruling`; `two-key` on a human commit to `evals/history/**` |
| `.claude/agents/<name>.md` | the seat in its `seat:` front matter | `ruling-cited` |

Every seat is one human (R1). No gate waits for a human approval; all
gates are mechanical and the list in SPEC/00 §5 is exhaustive:
`validate`, `signature`, `ruling-cited`, `two-key`, `regression`,
`cost-cap`, `docs-current`, `cold-review-ruling`, all as agentkeel §5
defines them, consumed at the pinned tag, plus one this repo adds:

- `cost-cap` (this repo's reading) — from M06 reads the interceptor
  ledger (`spend` table, per identity, per model), not Bedrock per-call
  metrics: a call through an inference target is billed to the gateway
  role, so per-request attribution exists only where the request
  interceptor wrote it. Before M06 it reads agentkeel's.
- `catch-rate` (from M04) — RED when the planted-error catch rate on the
  fixture set drops below `thresholds.yaml: catch_rate_min`, or when any
  planted error that was ever caught is no longer caught (regression bar
  on plants, P7). Read on `scope: pipeline` results only; the naive
  control is reported, never gated.

If a task seems to need a new gate, that is a SPEC/00 amendment, not a
workflow edit. Each ADR's `authorises:` names the seat whose rule it
changes. Every file on `main` has a seat; a file no seat owns is deleted.

## Subagents (`.claude/agents/`)

Call the seat subagent for the path you are changing before you open the
PR; paste its report into the PR body. The `product-spec-reviewer` report
goes into `milestones/MNN/feasibility.md` instead. Reports are drafts,
never rulings. Specialists (`bda-output-reviewer`, `rule-drafter`,
`red-teamer`, `docs-writer`, and `platform-architect` per §8 M01) exist
only from the milestone that added them; do not invoke one that is not
in the tree.

M00 only: `product-spec-reviewer` was written at the adoption PR and
run against SPEC/00 (`milestones/adoption/spec00-review.md`). PR 1
creates the other six seat subagents, so the "call before opening" rule
is waived for the six it cannot yet call. `product-spec-reviewer` runs
against SPEC/M00 before the rest of PR 1 is written.

Skills: `/open-milestone`, `/close-milestone`, `/cold-review`. From M01
on, a milestone opens and closes only through them.

## Where things are

```
SPEC/                 00-overview.md first (Product; amended only by ADR); MNN-*.md at each milestone's PR 1
.claude/              agents/ (each owned by the seat in its front matter; product-spec-reviewer at the adoption PR, the rest by milestone); skills/ (M00 close PR, Product)
docs/                 setup/ (bootstrap), adr/ (one ADR per rule change, max two amendments), milestones/ (explainers MNN.md; README.md generated, never hand-edited), video/, platform/ (overview.md M02; controls.md one row per measured control)
milestones/           README.md the ledger (one file); adoption/ (spec00-review.md, rulings/adopt-spec00.md); MNN/ (README.md, feasibility.md, rulings/, attestations.md, open.md)
src/                  baseline/ (M00 PR 1, frozen control at tag m00), verdict/ (schema.json, build.py, gate.py, replay_history.py), ingest/ (M01), triage/ (M04), review/ (M05, `ef review`), gateway/interceptors/ (M06, Security)
scripts/              seed_slate.py, plant_errors.py (M00 PR 1); render_cards.py, build_library.py (M02)
data/                 slate.json, spoiler_terms.json, titles/<title_id>/, plants/register.yaml (M00 PR 1); bda_coverage.yaml, media/manifest.yaml (M02); decisions/ (M05, CLI-written only)
tests/                fixtures/bda/ (by-hand BDA runs, raw), fixtures/plants/e-NNN/ (one commit each), test_verdict_disagree.py, test_control_card.py (M00 PR 1); rules/ (M04); gateway/ (M06)
evals/                goldens/v1/ (M00 PR 1, Data Owner), goldens/v2/ (M03), history/ (M00 PR 2, CI-written evidence), local/ (gitignored, not evidence)
rules/                M04, Rule Owner; <family>-<slug>.yaml per rule; answer-<slug>.yaml at M06
agents/               ingest/ (manifest.yaml M01; blueprints/ M02, field-level seats); search-media/ (M06: manifest.yaml with model_alias, prompt.txt, tools/, rules/)
tools/                M06, Tool Owner; kb-search/, title-metadata/, recommender/ schemas
infra/                Security except gateway/inference-targets.yaml (Threshold Owner, two-key, the only file with a provider model id); bootstrap/, construct/ (M01), gateway/ (M06), ruleset/main.json (M00 PR 2), workflows.sha256 (M01)
.github/              workflows/ (each gate at the PR that introduces it; reviewer-agents.yml advisory at M04); CODEOWNERS (M02)
Makefile              M00 PR 1, Engineering; all five targets exist from M00 PR 1
uv.lock               M00 PR 1, Engineering
thresholds.yaml       M03, Threshold Owner; two-key on any downward move; daily_usd_per_user from M06
```

## Commands

```
make evals            baseline, plus search-media from M06, against goldens, CI-equivalent
make evals-local      same, your credentials, writes evals/local/ only
make validate         grows by milestone; the ledger header says what it
                      checked at each tag. M00: golden and ruling front
                      matter. M01+: schema, seats, edges, semver, cdk-nag
make plants           list plants and whether each fired on last run
make ledger           print the ledger with measured values; exits 1 if a
                      Measured cell differs from its envelope
make ledger-plain     the same, and writes docs/milestones/README.md
ef review             M05
```

Until M00 PR 2, `evals`, `plants` and `ledger` exit 1 with
"not until M00 PR 2".

## Writing

Plain. Short sentences. Name the shortcoming. Numbers over adjectives.
The explainer pages and the README are read by directors; if a sentence
needs the envelope schema to be understood, it belongs in `docs/platform/`
not in an explainer.

## When unsure

Say so in the PR body under **Unsure**, name the seat whose ruling would
settle it, and stop. An unstated assumption that later proves wrong costs
a milestone; a stated one costs a sentence.
