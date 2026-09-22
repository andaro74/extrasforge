---
adr: ADR-0001
title: SPEC/00 adopted; rulings R1–R13 recorded
status: Accepted
date: 2026-09-22
seat: Product
authorises:
  - Product
platform: agentkeel
platform_version: m00
amendments: 1
---

# ADR-0001 — SPEC/00 adopted

## Context

SPEC/00-overview.md (rev 2) was written and reviewed before the repo had
anything else in it. It was pushed in the bootstrap commit and is amended
only by ADR from that point. The adoption PR (#1) wrote the
`product-spec-reviewer` subagent first, ran it against SPEC/00, and
pasted the report into `milestones/adoption/spec00-review.md`
(`BLOCK: 1 · FINDING: 33 · NOTE: 6`). Where the report found a false
state not named, a cut list unordered, or a sentence that needs the
envelope schema to be understood, the PR #1 body proposed the SPEC/00
diff under Proposed amendments (PA-1 to PA-12). Product ruled on each on
2026-09-22; the rulings are amendment 1 below and are applied to SPEC/00
(rev 3) by PR #2.

## Decision

SPEC/00-overview.md is adopted as the authority for this repo. CLAUDE.md
is the working summary; where they disagree SPEC/00 wins and CLAUDE.md
gets a PR.

This repo is a tenant of agentkeel at tag `m00`. It consumes agentkeel's
`GovernedAgent` construct, seat model, envelope schema
(`verdict.schema.json`) and gates at that tag and adds nothing to them
but the envelope fields SPEC/00 §6 lists, each by ADR (amendment 1,
item 1). The tag is recorded here and in every manifest's
`platform_version`.

Rulings R1–R13 (SPEC/00 §11) are recorded here and get no ruling files:

- R1 one human; the mechanical gates in §5 are exhaustive, plus
  `catch-rate` from M04 and this repo's reading of `cost-cap` from M06.
- R2 `passed == total` is not a gate anywhere.
- R3 two accounts with boundaries; account-per-team is a follow-on.
- R4 keys, bootstrap and cosign identity belong to Security.
- R5 evidence retention seven years, Object Lock, security account.
- R6 the judge is a model too: pinned, watched, never the model under test.
- R7 computed semver; schema or edge change is major.
- R8 subagents by need; `product-spec-reviewer` at the adoption PR, the
  other six seat subagents at M00 PR 1, specialists at the milestone in
  their "Added at" column (§5.1).
- R9 cold review from the start; the ruling file is written for every
  PR; `cold-review-ruling` enforces it from M00 PR 2.
- R10 (this repo's N) N is the review SLA, 24 hours; an item past N
  escalates, never auto-approves.
- R11 golden ids are immutable.
- R12 fictional slate, stand-in footage, interview cut list: no real
  title, cast, contract or studio workflow detail; footage is CC-BY
  open-movie content with title cards trimmed and fictional cards
  rendered on; the cut order is M07 first (written drill, one measured
  swap), M05 second (CLI alone); M00–M04 and M06 are never cut.
- R13 one model path: every model call goes through the AgentCore
  Gateway inference target; an agent manifest carries a `model_alias`,
  never a provider model id; a provider model id appears in
  `infra/gateway/inference-targets.yaml` and nowhere else.

## The one planned departure from agentkeel

ADR-0007 (inference target, M06 PR 1) is the one planned departure from
agentkeel at tag `m00`: the model path is an AgentCore Gateway inference
target in Provider form (SPEC/00 §2, §9.7, R13), not agentkeel's
LLM-gateway alias. Nothing before M06 departs. agentkeel adopts the same
path at its own upgrade milestone; it is never retrofitted here.

## Amendment 1 (Product rulings on the Proposed amendments of PR #1; applied by PR #2)

Each item names the PR #1 proposal it settles, the SPEC/00 section it
changes, and the report finding it answers
(`milestones/adoption/spec00-review.md`).

1. **PA-1, the BLOCK — envelope extension lands at M00 PR 1.** §6
   "Envelope": extended by ADR at M00 PR 1 (the copy in
   `src/verdict/schema.json`) with `scope`, `plants_expected`,
   `plants_fired` and `checks`; at M03 with `calibration{field: ece}`;
   at M04 with `catch_rate` and `catch_by_layer{}`. §2 admits the
   envelope fields as a stated exception to "adds nothing". (Check 1
   BLOCK)
2. **PA-2 — frozen control has a falsifier and a reader.** §8 M00 adds
   F0.4: a commit after tag `m00` changes `src/baseline/**` and
   `validate` is GREEN; from the M00 close PR `validate` compares the
   tree hash of `src/baseline/**` to the one ADR-0002 records. (Check 1)
3. **PA-3 — `validate` scope at M00.** §8 M00: golden and ruling front
   matter at M00; rule front matter and the `planted_error` ↔ register
   check from M04. (Check 1)
4. **PA-4 — terminal ledger states named.** §6 "Job ledger": `analysed`
   at M01; `indexed` and `quarantined` from M04; `reviewed` from M05.
   (Check 1)
5. **PA-5 — M03 plant is a fixture.** §8 M03 Seeded: a fixture copy
   under `tests/fixtures/plants/` with one field's confidence values
   raised and its labels unchanged. Data Owner names the path at M03
   PR 1. (Check 1)
6. **PA-6 — register kinds for `e-009` and `e-010`.** §6 register
   `kind` enum gains `wrong_speaker` and `placeholder`. (Check 1)
7. **PA-7 — §14 lists M01 and M04 BDA calls.** M01's pipeline runs on
   the four fixtures (once per seeded case) and M04's strict re-runs on
   quarantined fixtures are within the cost statement. (Check 1)
8. **PA-8 — the strict-blueprint re-run is dropped from M04.** §8 M04
   Build reads "three-band triage in `src/triage/` (verified / queue /
   quarantine)". If M04 needs a strict blueprint it is defined under §5
   ownership notes by ADR at M04 PR 1. (Check 1)
9. **PA-9 — cut list ordered.** §8 preamble and R12: 1. M07 closes as
   a written drill with one measured swap, the breaking swap (F7.1),
   F7.2 and F7.3 recorded RED-by-cut; 2. M05 closes on the CLI alone,
   F5.3 recorded RED-by-cut. M00–M04 and M06 are never cut. (Check 6)
10. **PA-10 — M00 Done-when in the plain register.** One plain clause
    first ("the naive pipeline let all 10 planted errors through and
    cited nothing"), the envelope line under it. (Check 5)
11. **PA-11 — a Done-when line per milestone.** §8 preamble: each
    SPEC/NN PR 1 adds the milestone's Done-when line to §8 MNN by ADR,
    plain clause first, measured line under it. (Check 5)
12. **PA-12 — title and §1 sentence reworded.** Title: "media
    ingestion under seat-owned rules, to agentic package curation".
    §1: "tested by a seeded error" for "proved by". README sentence:
    "BDA describes the media; extrasforge shows that a description
    reaches the knowledge base only by passing a rule one named person
    owns, and that the agent cannot state a machine-written description
    as fact." CLAUDE.md's opening sentence follows. (Check 5)
13. **Seat subagent timing.** §5.1: `product-spec-reviewer` at the
    adoption PR, run against this SPEC; the other six at M00 PR 1. This
    matches `docs/setup/structure.md` and what PR #1 did. CLAUDE.md's
    Subagents section names this repo's specialists
    (`bda-output-reviewer`, `rule-drafter`, `red-teamer`, `docs-writer`,
    `platform-architect` per §8 M01), not agentkeel's. (Check 7)

Also changed to match: the SPEC/00 status line reads ADOPTED (rev 3)
and names tag `m00`; CLAUDE.md's `make evals` line names `search-media`
from M06, and its "Never touch" line names `data/titles/` and
`data/plants/` in place of agentkeel's `data/corpus/`.

Not settled by this amendment: the remaining 22 FINDINGs and 6 NOTEs in
`milestones/adoption/spec00-review.md` (checks 2, 3, 4, 7 and 8). Each
is ruled at the PR 1 of the milestone it concerns, by ADR. This ADR has
used its one amendment; the next change to these rulings is a new ADR.

## Consequences

- SPEC/00 §8 MNN is the ruling for each milestone's build paths, cited
  as `SPEC/00-overview.md#8-MNN`.
- `platform_version: m00` is the pin every manifest and `validate` check
  reads; moving it is a Product ruling with an ADR.
- M00 PR 1 copies `verdict.schema.json` from agentkeel at `m00` and
  extends it with the four fields of item 1, under its own ADR.
- The M00 close PR's ADR-0002 records the tree hash of `src/baseline/**`
  that F0.4 reads.
