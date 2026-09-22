# product-spec-reviewer report — SPEC/00 rev 2 at adoption

Read: `SPEC/00-overview.md` (all), `CLAUDE.md` (exists on disk, untracked; read because the prompt says to), `docs/adr/ADR-0001-spec00-adopted.md` (named in the SPEC status line). No `milestones/README.md`, no `SPEC/NN`. Checks 1–4 and 6–7 run per §8 milestone; 5 and 8 once.

## Check 1 — False state

**BLOCK** · §2, §6, §8 M00, §8 M03. §2: "consumes its `GovernedAgent` construct, seat model, envelope schema and gates at a pinned tag and adds nothing to them." §6: "Envelope: agentkeel's `verdict.schema.json` at the pinned tag, extended by ADR at M04 with `scope ∈ {control, pipeline, agent}`, `plants_expected`, `plants_fired`, `catch_rate`, `catch_by_layer{}`, `calibration{field: ece}`, and `checks` keyed by falsifier id." §8 M00: "Every result is `scope: control` ... `plants_expected = 0` under the plant rule ... `checks.F0_2` and `checks.F0_3` decide the verdict." §8 M03: "expected calibration error on the envelope". M00 PR 2 and M03 PR 2 must write and read envelope fields the spec says do not exist until M04 and that §2 says this repo never adds. Product rules where the envelope extension lands (M00 PR 1 with `verdict.schema.json`, or M04) and amends §2 to admit it; Product seat.

**FINDING** · §8 M00 "Frozen by ADR-0002 at the close PR." and §7 row 0 "a frozen naive ingest". The false state is nameable today (a commit to `src/baseline/` after tag `m00`), but no falsifier F0.x and no gate in §5 R1 reads it; CLAUDE.md carries it only as a "Never". Product adds a falsifier and names the gate (`validate` on a path hash, or `regression`); Product, with Security for the gate.

**FINDING** · §8 M00 "`validate` checks golden, rule and ruling front matter and that every `planted_error` id in a rule exists in the register" and "Reads nothing from `rules/`". No rule can exist before M04, so at M00 the rule half of `validate` has no plantable false state. State which `validate` checks are live at M00 (golden and ruling only) and which arrive at M04; Product.

**FINDING** · §8 M01 "Every upload reaches one terminal ledger state" and §6 "state ∈ {submitted, analysed, validated, indexed, quarantined, reviewed}". No section says which states are terminal, and at M01 only `submitted` and `analysed` can exist, so F1.4 cannot be written. Name the terminal set per milestone in §6; Product, Engineering drafts.

**FINDING** · §8 M03 "Seeded: a blueprint revision that raises a field's confidence without raising its accuracy". BDA's confidence output is not under the planter's control, so the plantable false state is an edited output fixture, not a blueprint revision. Say the plant is a fixture with edited confidence values and name its path; Data Owner.

**FINDING** · §9.4 "`e-010` blueprint field filled with "N/A" where `unknown` is required" against §6 "`kind ∈ {wrong_person, invented_character, timestamp_past_duration, overlap, wrong_title_card, spoiler_term, wrong_language, runaway_summary, wrong_title_prefix, injection}`". `e-010` (and arguably `e-009` "audio speaker label not in crew") has no `kind` in the enum, so `validate` front-matter check on the register fails or the enum is bypassed at PR 1. Add a kind (e.g. `placeholder`) to §6; Data Owner.

**FINDING** · §8 M01 "EventBridge → `start_analysis` Lambda → BDA async job" and §8 M04 "re-run with the strict blueprint" against §14 "BDA calls confined to the four M00 fixtures (by hand), M02's library build (once), M03's golden labelling run (once) and M07's swap (once)." M01's pipeline and M04's re-run both make BDA calls §14 does not allow, so either the seeded cases cannot run or §14 is wrong. Amend §14 to list M01 and M04 runs; Product, Threshold Owner on the cap.

**FINDING** · §8 M04 "re-run with the strict blueprint". No section defines a strict blueprint, its path or its seat, so the quarantine re-run has no plantable input. Define it under §5 ownership notes or drop the re-run; Product, with Data Owner and Rule Owner on the blueprint fields.

## Check 2 — Reader

**FINDING** · §8 M00 "a run without a control card is rejected by `verdict.build`" and "`checks.F0_2` and `checks.F0_3` decide the verdict." An envelope `verdict.build` wrote always has a control card, so `checks.F0_2` can never be false in any envelope the gate reads; the real reader of F0.2 is a test (`tests/test_control_card.py` in CLAUDE.md) the SPEC never names. Name the test as the reader and drop `checks.F0_2` from the verdict; Product, Engineering drafts.

**FINDING** · §5 Threshold Owner "judge rubric, judge model id" and §8 M06 F6.3 "the injection changes an answer or a judge score". No §8 build line builds a judge; the only candidate is M03's "entailment check (summary vs transcript + OCR)", never called a judge. Say which milestone builds the judge and whether the entailment check is it; Product, Threshold Owner on the rubric.

**FINDING** · §8 M06 F6.2 "the answer states the generated field as fact" read by both §9.3 `answer` "no `generated` field stated as fact" and §13 "Bedrock Guardrails | contextual grounding at the answer". Two mechanisms read one claim; if the guardrail's own BLOCKED is what the gate reads, the component that blocks also reports its pass. Name the one reader for F6.2 and make the other reported-only; Rule Owner, with Threshold Owner on the guardrail.

**FINDING** · §8 M06 F6.6 "the over-budget identity is served, or is served and not on the ledger" and §9.7 "the request interceptor writes identity, alias and resolved model to the `spend` ledger ... `cost-cap` reads that ledger." A call the interceptor failed to write is invisible to the ledger the gate reads, so "served and not on the ledger" cannot be observed from the named reader. Name an independent count (the OTel spans M06 already builds) that the gate compares to ledger rows; Security.

**FINDING** · §8 M07 F7.4 "rollback needs a code or workflow edit." and F7.5 "the swap PR that touches anything but the mapping merges." No gate in §5 R1 reads a PR's touched-path set, and M07 may close as "a written drill", so the reader may never land. Name the gate (`two-key` on path set, or `validate` on the diff) and its milestone; Security.

**FINDING** · §8 M04 F4.5 "a judge check is added for something a rule in the tree already catches (P12)." Nothing mechanical can observe this; it is a review judgment. Either drop F4.5 to a `rule-test-runner` advisory or name the file/field a check would compare; Rule Owner.

## Check 3 — Falsifiers

**FINDING** · §8 M02 F2.2 "a generated field carries `extractive`." The truth ("BDA documents as generated") lives in BDA's documentation, not in the repo, so no reader can observe F2.2 mechanically. Commit a per-modality provenance truth file the coverage file is diffed against, and name its seat; Data Owner.

**FINDING** · §8 M02 F2.3 "the viewer shows a timestamp the output does not contain." No test, check or gate is named for a static page. Name the test or drop the falsifier; Engineering drafts, Product rules.

**FINDING** · §8 M01 F1.4 "any asset ends in a non-terminal state after N." N is the review SLA (R10, 24 hours), reused as an ingest timeout, and CI cannot wait 24 hours, so the observation needs an injectable clock the spec does not mention. State the ingest timeout separately from N and how CI observes it; Threshold Owner on the value, Engineering on the clock.

**FINDING** · §8 M05 "Seeded: ... a known-answer seed answered wrong." None of F5.1–F5.4 names what that seed answered wrong would show. Add F5.5 with the envelope field (`reviewer_accuracy`) it lowers; Product.

**FINDING** · §8 M07 "Seeded: a blueprint version that drops the `characters` enum; a model swap known to break citation format; an equivalent swap" against §8 preamble "M07 may close as a written drill with one measured swap." With one measured swap, at most one of F7.1 and F7.2 can be observed; the other falsifier is unobservable by ruling. Say which swap is the measured one and mark the other RED-by-cut; Product.

**FINDING** · §8 M00 "3 leak" goldens and "leak goldens 0/3 BLOCKED and `never_passed`" observed only at M06 "query and leak goldens measured." The observation of a golden seeded at M00 depends on M06, six milestones later, and R12 puts M06 last in the set. Record it as such in the M00 ledger row so the cell is not read as a M00 miss; Product.

## Check 4 — Expected gate output

**FINDING** · §8 M01–M07: none has an "Expected" line; only M00 states "10/10 plants pass through ... `cites` false on all 5; leak goldens 0/3". For seven milestones the expected number is not stated before the run, and the SPEC/NN that could carry it does not exist yet. Require the number in each SPEC/NN PR 1 or add it to §8 now; Product.

**FINDING** · §8 M03 "a blueprint change that moves calibration goes RED" (§7 row 3). No ECE delta bar is stated anywhere, so RED has no number. State the ECE bar (or its `thresholds.yaml` key) before M03 PR 1; Threshold Owner.

**FINDING** · §5 "`thresholds.yaml: catch_rate_min`" and §8 M04 "the rulebook catches its share, the entailment check its share". `catch_rate_min` has no value and `thresholds.yaml` lands at M03, so M04's delta against 10/10 has no stated bar; per-layer shares are in the register, the aggregate is not. State the value; Threshold Owner.

**FINDING** · §8 M06 "Seeded: ... a query whose only evidence is a `generated` field" against §6 golden "query: `asset_ids[]`, `cited_timestamps[]`". The golden schema has no expected value for "must not state as fact", so it is unclear whether F6.2's outcome is a delta or a falsifier. Add an expected form (`REFUSED` or a `guardrail`-kind golden) to §6; Data Owner.

**FINDING** · §6 golden "kind ∈ {ingest, query, leak, guardrail, redteam}" — no §8 line seeds a `guardrail` or `redteam` golden, and M06's F6.3 needs a redteam golden with an expected block. Name the milestone and count for both kinds; Data Owner.

**FINDING** · §3 "100% confirm at 4 seconds per item" and §8 M05 "a reviewer that confirms 20 items in 60 seconds" against §5 Threshold Owner "`thresholds.yaml` (triage bands per field, calibration method, catch-rate bar, cost cap, per-user budget)". The rubber-stamp bar (seconds per item, confirm rate) is in no owned file, so F5.1 has no number and no seat for the number. Add the two keys to the `thresholds.yaml` list; Threshold Owner.

## Check 5 — Plain sentence

**FINDING** · §1 title "governed media ingestion" and §1 "with every hallucination control proved by a seeded error" and the README sentence "extrasforge proves which descriptions could only have reached the knowledge base by passing a rule a seat owns". "governed" and "proves"/"proved" describe controls none of which has fired; "a rule a seat owns" and "generated description" need §5 and P13 to be understood. Reword to what is measured at the tag the reader is at, and re-check at each close; Product.

**FINDING** · §8 M00 "Done when: `make evals` writes an envelope with the control card and the row 0 measured value (`plants_through 10/10; cites 0/5; leak 0/3 never_passed`)". "envelope", "control card", "plants_through", "never_passed" need §6 and the plant rule; a director cannot read the line. Give M00 a one-clause plain Done-when ("the naive pipeline let all 10 planted errors through and cited nothing") with the envelope line under it; Product.

**FINDING** · §8 M01–M07: no milestone but M00 has a "Done when" line. Seven milestones close on §7's claim text alone, which is not a director sentence. Add one Done-when per milestone before its PR 1; Product.

**NOTE** · §6 "Job ledger" and §8 M00 "the ledger; `replay_history`" and CLAUDE.md "`make ledger` print the ledger with measured values". "ledger" names the DynamoDB job ledger, the `spend` ledger and `milestones/README.md`. Weak because a reader of "Done when ... in `milestones/README.md`" cannot tell which; qualify each use; Product.

## Check 6 — Cut list and cap

**FINDING** · §8 preamble "Cut list for the interview deadline (R12): M00–M04 and M06 are the set; M05 may close on the CLI alone; M07 may close as a written drill with one measured swap." The list is not ordered (which of M05, M07 is cut first is unstated) and both cuts drop seeded cases: M05 "on the CLI alone" cannot observe F5.3 ("does not change the index") and M07 "one measured swap" drops two of three seeded swaps. Order the cuts and either keep every seeded case or mark each dropped one RED in the ledger row; Product.

**FINDING** · §8 M06 build: agent on `GovernedAgent`, gateway, three MCP targets, inference target, model provider, role without `InvokeModel`, request interceptor, response interceptor, `answer` rules, "OTel to CloudWatch GenAI Observability with the resolved model on every span", goldens measured, ADR-0007, `red-teamer`, `tools/**` schemas — 13 items, no cut list, four PRs with PR 1 plant-only and PR 4 close. OTel has no falsifier and the title-metadata and recommender tools have none, so those threaten the cap first. State a cut order for M06 (OTel last-in, two tools optional); Product.

**FINDING** · §8 M04 build against §9.5 "Reviewer agents in CI (advisory; from M04) `security-lint` ... `rule-test-runner` ... `spoiler-term-scan`" and CLAUDE.md "reviewer-agents.yml advisory at M04". §8 M04 does not list the three CI reviewer agents, so M04 carries ~10 items plus the rules of five families (§9.3 lists 19 rule lines) with no cut list. Either list them in §8 M04 with a cut order or move them to M05; Product.

**FINDING** · §8 M02 build: ~80 media items through BDA, three custom blueprints, coverage file, "the viewer (one static page from the insights bucket: scenes and timestamps, transcript spans, OCR, bounding boxes, diarization)", plus CLAUDE.md's `render_cards.py`, `build_library.py`, `CODEOWNERS`, `docs/platform/overview.md` and the M02 executive explainer with the §12 KB-multimodal comparison. The viewer's bounding boxes and diarization have no falsifier and threaten the cap. Cut the viewer to scenes, timestamps and OCR (the three F2.3 can read); Product.

## Check 7 — Seats

**FINDING** · §5 Engineering "`src/**`" and Security "gateway interceptors (`src/gateway/interceptors/**`)". One path, two seats in SPEC/00; CLAUDE.md carves it out ("`src/**` (but for `src/gateway/interceptors/**`)") but SPEC/00 wins. Add the carve-out to §5 Engineering; Product.

**FINDING** · §5 Rule Owner "guardrail id/version in manifest" and §6 inference-target config "one entry per alias with provider, model id + version + region, guardrail id" owned by Threshold Owner. The guardrail id is a field in two files with two seats and no rule for which is authoritative when they differ. Name one file as the source and make the other a `validate`-checked copy; Product, with Rule Owner and Threshold Owner.

**FINDING** · §6 Manifest "`model_alias` ..., seats → groups, `may_call` ..., endpoint allowlist, ceilings, `platform_version`". `model_alias`, endpoint allowlist, ceilings and `platform_version` have no seat in §5 (Threshold Owner owns "agent model id", not the alias; ADR-0001 says moving `platform_version` is a Product ruling, §5 does not). Assign each manifest field a seat in the §5 ownership notes; Product.

**FINDING** · CLAUDE.md "`.github/` workflows/ ...; CODEOWNERS (M02)" against §5 Security "`.github/workflows/**`". `.github/CODEOWNERS` has no seat in §5 and §5 says "A file no seat owns is deleted." Add it to Security or Product in §5; Product.

**FINDING** · §8 M01 "Adds `platform-architect`" against §5.1 specialists table (`bda-output-reviewer`, `rule-drafter`, `security-reviewer`, `red-teamer`, `docs-writer`) and CLAUDE.md "Specialists (`platform-architect`, `red-teamer`, `docs-writer`, `legal-compliance`, `incident-responder`)". `platform-architect`, `legal-compliance` and `incident-responder` have no row (caller, seat, duty) in §5.1, and `bda-output-reviewer` and `rule-drafter` are absent from CLAUDE.md, so R8 ("added by need") cannot be checked. Reconcile the two lists in §5.1 and give each a calling seat; Product.

**NOTE** · §5 ownership notes "enum and `unknown` handling is Rule Owner; which fields exist and which are `generated` is Data Owner". An enum field's existence is Data Owner and its values Rule Owner, so one blueprint field has two seats. Weak because the diff rule ("names the seat of every field it changes") papers over it; state the tie-break; Product.

## Check 8 — Contradictions

**FINDING** · §8 M00 "Done when: `make evals` writes an envelope ... and the row 0 measured value ... in `milestones/README.md`" against CLAUDE.md "PR 4 — close. Ledger 'measured' cell" and "`make ledger` ... exits 1 if a Measured cell differs from its envelope". SPEC has `make evals` writing the ledger cell; CLAUDE.md has a human writing it at PR 4 with `make ledger` checking it. Say who writes the cell; Product.

**FINDING** · §5 R1 "plus one this repo adds from M04:" followed by two bullets, the first "`cost-cap` (this repo's reading) — from M06". One announced, two listed, one dated M06 not M04; ADR-0001 states it correctly ("plus `catch-rate` from M04 and this repo's reading of `cost-cap` from M06"). Match §5 to ADR-0001; Product.

**NOTE** · Status line "Rulings R1–R13 recorded at open" against §8 M00 "This SPEC/00 and R1–R12, recorded by ADR-0001"; §11 lists R1–R11 as agentkeel's then R10 as "this repo's N", and orders R10, R13, R12. R13 is dropped from the M00 line and R10 is both adopted unchanged and repo-specific. Weak because ADR-0001 has all thirteen; fix §8 M00 and §11 order; Product.

**NOTE** · CLAUDE.md "Never let the corpus that judges an answer also supply the answer. (`validate` checks golden/retrieval overlap; do not route around it.)" against §8 M00 `validate` scope and §8 M04 `validate` scope. No SPEC/00 line gives `validate` a golden/retrieval overlap check. Weak because it can ride in M06; name the milestone; Product.

**NOTE** · §8 M00 "`verdict.build`, `verdict.gate` (with the plant rule as one line at PR 2)" and "Seeded: ... a run without a control card is rejected by `verdict.build`" against CLAUDE.md "PR 1 — plant ... Nothing that makes it pass." Whether `verdict.build` lands in PR 1 (so the seeded rejection is in the tree) or PR 2 is not stated. Weak because either reading is workable; state the PR; Engineering drafts, Product rules.

**NOTE** · §15 "at least six are GREEN" against §8 preamble cut list (M00–M04 and M06 = six milestones). The two combine to zero slack: any one RED among the six fails §15. Weak because it may be intended; say so in §15; Product.

BLOCK: 1 · FINDING: 33 · NOTE: 6
