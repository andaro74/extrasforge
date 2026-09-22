---
ruling: DRAFT
seat: Product
authorises:
  - SPEC/00-overview.md
  - CLAUDE.md
  - docs/adr/ADR-0001-spec00-adopted.md
evidence:
  - milestones/adoption/spec00-review.md
  - docs/adr/ADR-0001-spec00-adopted.md
  - https://github.com/andaro74/extrasforge/pull/1
pr: 2
---

# Ruling: amend SPEC/00 at adoption (ADR-0001 amendment 1)

Product ruled on the twelve Proposed amendments in the PR #1 body on
2026-09-22 and accepted all twelve, plus one ruling on seat-subagent
timing raised under Unsure. The rulings are ADR-0001 amendment 1. This
PR applies them to SPEC/00 (rev 3) and to CLAUDE.md, and marks ADR-0001
Accepted. ADR-0001 has used its one amendment.

This PR carries its ruling in-PR. The `cold-review-ruling` check does not
exist yet (R9: required from M00 PR 2). This PR is not M00 PR 1 and is
not counted against M00's cap of four. Its ruling for the three paths it
touches is `milestones/adoption/rulings/adopt-spec00.md`, whose
`authorises:` names each of them.

`ruling: DRAFT` is changed to the ruling by the Product seat at merge.

What a reader can falsify:

- Every numbered item in ADR-0001 amendment 1 appears in this PR's diff
  of SPEC/00-overview.md or CLAUDE.md. `git diff 244454c -- SPEC/00-overview.md CLAUDE.md`
  shows no hunk that amendment 1 does not name.
- Each item names the PR #1 proposal (PA-n) it settles; the PR #1 body
  carries the proposal text.
- The 22 FINDINGs and 6 NOTEs amendment 1 leaves open are still in
  `milestones/adoption/spec00-review.md` unchanged; that file is not
  touched by this PR.
