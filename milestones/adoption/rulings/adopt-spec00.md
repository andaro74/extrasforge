---
ruling: DRAFT
seat: Product
authorises:
  - SPEC/00-overview.md
  - CLAUDE.md
  - docs/adr/ADR-0001-spec00-adopted.md
  - .claude/agents/product-spec-reviewer.md
evidence:
  - milestones/adoption/spec00-review.md
pr: 1
---

# Ruling: adopt SPEC/00

SPEC/00-overview.md (rev 2) is adopted as the authority of this repo, at
agentkeel tag `m00`, by `docs/adr/ADR-0001-spec00-adopted.md`. R1–R13
are recorded there and get no ruling files.

This PR carries its ruling in-PR. The `cold-review-ruling` check does not
exist yet (R9: required from M00 PR 2). This PR is not M00 PR 1 and is
not counted against M00's cap of four.

`ruling: DRAFT` is changed to the ruling by the Product seat at merge.
The Proposed amendments in the PR body are not applied by this PR;
Product's ruling on each fills ADR-0001 amendment 1, and the PR that
carries that amendment is the one that edits SPEC/00.

What a reader can falsify:

- `milestones/adoption/spec00-review.md` is the `product-spec-reviewer`
  report, pasted verbatim. Re-running the subagent in
  `.claude/agents/product-spec-reviewer.md` with input `adoption` against
  SPEC/00 at this PR's merge commit reproduces its findings; the
  severity counts on its last line are the ones the PR body cites.
- SPEC/00-overview.md is byte-identical to the bootstrap commit
  (`ee618cd`). `git diff ee618cd -- SPEC/00-overview.md` on this PR's
  head is empty.
- CLAUDE.md differs from agentkeel's at tag `m00` only in the items the
  PR body lists under CLAUDE.md. `git diff` against
  `../agentkeel` at `m00` shows nothing else.
- Every path this PR touches has a seat in the PR body's table, and
  every one of them is Product's or, for the subagent file, the seat
  in its front matter (Product).
