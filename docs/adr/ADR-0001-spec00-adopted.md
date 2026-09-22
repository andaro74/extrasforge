---
adr: ADR-0001
title: SPEC/00 adopted; rulings R1–R13 recorded
status: Proposed
date: 2026-09-21
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
only by ADR from that point. The adoption PR (#1) writes the
`product-spec-reviewer` subagent first, runs it against SPEC/00, and
pastes the report into `milestones/adoption/spec00-review.md`. Where the
report finds a false state not named, a cut list unordered, or a
sentence that needs the envelope schema to be understood, the PR body
proposes the SPEC/00 diff under Proposed amendments. Product rules on
each; the rulings fill amendment 1 below.

## Decision

SPEC/00-overview.md is adopted as the authority for this repo. CLAUDE.md
is the working summary; where they disagree SPEC/00 wins and CLAUDE.md
gets a PR.

This repo is a tenant of agentkeel at tag `m00`. It consumes agentkeel's
`GovernedAgent` construct, seat model, envelope schema
(`verdict.schema.json`) and gates at that tag and adds nothing to them.
The tag is recorded here and in every manifest's `platform_version`.

Rulings R1–R13 (SPEC/00 §11) are recorded here and get no ruling files:

- R1 one human; the mechanical gates in §5 are exhaustive, plus
  `catch-rate` from M04 and this repo's reading of `cost-cap` from M06.
- R2 `passed == total` is not a gate anywhere.
- R3 two accounts with boundaries; account-per-team is a follow-on.
- R4 keys, bootstrap and cosign identity belong to Security.
- R5 evidence retention seven years, Object Lock, security account.
- R6 the judge is a model too: pinned, watched, never the model under test.
- R7 computed semver; schema or edge change is major.
- R8 subagents by need; seven seat subagents at M00, specialists at the
  milestone in their "Added at" column (§5.1).
- R9 cold review from the start; the ruling file is written for every
  PR; `cold-review-ruling` enforces it from M00 PR 2.
- R10 (this repo's N) N is the review SLA, 24 hours; an item past N
  escalates, never auto-approves.
- R11 golden ids are immutable.
- R12 fictional slate, stand-in footage, interview cut list: no real
  title, cast, contract or studio workflow detail; footage is CC-BY
  open-movie content with title cards trimmed and fictional cards
  rendered on; the milestone set for the interview deadline is M00–M04
  and M06; M05 may close on the CLI, M07 as a written drill with one
  measured swap.
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

## Amendment 1 (rulings on the Proposed amendments of PR #1)

Filled by Product's rulings on the Proposed amendments in the PR #1 body.
Each item is applied to SPEC/00 by the PR that carries this ADR to
`status: Accepted`, never silently.

(none yet)

## Consequences

- SPEC/00 §8 MNN is the ruling for each milestone's build paths, cited
  as `SPEC/00-overview.md#8-MNN`.
- `platform_version: m00` is the pin every manifest and `validate` check
  reads; moving it is a Product ruling with an ADR.
- Every amendment to SPEC/00 from this point is an ADR; a second
  amendment to this ADR is its last, a third change is ADR-0003 or later.
