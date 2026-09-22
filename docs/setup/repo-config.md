# extrasforge — repository description and initial configuration

## GitHub description (the one-line field)

> Governed media ingestion on Amazon Bedrock Data Automation — BDA describes the media, seat-owned rules decide what is fact, ten planted errors prove it. A tenant of agentkeel. Fictional slate.

Topics: `amazon-bedrock`, `bedrock-data-automation`, `agentcore`, `strands-agents`, `knowledge-bases`, `mcp`, `ai-governance`, `hallucination-detection`, `media-supply-chain`, `aws-cdk`

## Repository settings

| Setting | Value | Why |
|---|---|---|
| Visibility | Public | it is a portfolio artefact |
| Default branch | `main` | |
| Merge button | **Merge commits only** — squash and rebase disabled | envelopes are keyed to measured commit hashes; bot authorship under `evals/history/` is evidence (agentkeel ADR-0004 amendment 1) |
| Auto-delete head branches | on | |
| Issues | on; Wiki, Projects, Discussions off | one ledger, not three |
| Git LFS | on; `docs/video/**/*.mp4` only | milestone videos |
| Actions | allowed; **read-only `GITHUB_TOKEN` default** | CI writes only through the workflow-scoped permissions set per job from M00 PR 2 |
| Actions: fork PR workflows | require approval | |
| Dependabot alerts | on; Dependabot version updates off | version bumps to Engineering paths are ordinary PRs; no bot opens PRs into seat-owned paths |
| Secrets | none at bootstrap | AWS access is GitHub OIDC → deploy role at M01; no long-lived keys, ever |

## Branch protection on `main` (a ruleset, exported to `infra/ruleset/main.json` at M00 PR 2)

- Require a pull request before merging; 0 required approvals (R1 — one human; no gate waits for a human).
- Require status checks to pass; **required checks: none at adoption**, then added at the PR that introduces each: `validate` (M00 PR 1 as a workflow-less `make validate` run locally; required from M00 PR 2), `cold-review-ruling` (M00 PR 2), `regression` and `cost-cap` (M00 PR 2), `ruling-cited` and `two-key` (M02), `catch-rate` (M04), `docs-current` (M03).
- Require linear history: **off** (merge commits are the shape).
- Block force pushes; block deletions.
- Bypass list: **empty**. No actor, including the owner, can bypass. P9.
- Restrict who can push to `main`: only via PR.

Set through the UI or with `gh api` (below). The ruleset's JSON is committed at M00 PR 2 so a change to it is a Security-seat PR with a diff.

## Initial tree (adoption PR #1 adds CLAUDE.md, ADR-0001, the reviewer subagent)

```
extrasforge/
  README.md
  LICENSE                 MIT
  NOTICE                  CC-BY attributions, filled per fixture PR
  .gitignore              evals/local/, media binaries, venv, caches
  .gitattributes          LFS for docs/video/**/*.mp4; LF everywhere
  .python-version         3.12
  pyproject.toml          uv-managed; boto3, pyyaml, jsonschema; pytest, ruff
  SPEC/
    00-overview.md        the authority
  milestones/             created at M00 PR 1 (ledger) and adoption PR (rulings)
  docs/
    setup/repo-config.md  this file
```

Everything else (`src/`, `data/`, `rules/`, `evals/`, `tests/`, `agents/`, `infra/`, `.github/`, `.claude/`) is created by the PR SPEC/00 assigns it to. A file on `main` before its PR is a file no seat has authorised.

## Bootstrap commands

```bash
# 1. create the repo (public, no template, no auto-README)
gh repo create andaro74/extrasforge --public \
  --description "Governed media ingestion on Amazon Bedrock Data Automation — BDA describes the media, seat-owned rules decide what is fact, ten planted errors prove it. A tenant of agentkeel. Fictional slate." \
  --disable-wiki

# 2. local init from the files in this folder
cd extrasforge
git init -b main
git lfs install
git add README.md LICENSE NOTICE .gitignore .gitattributes .python-version pyproject.toml SPEC/00-overview.md docs/setup/repo-config.md
git commit -m "bootstrap: SPEC/00, licence, notice, root config"
git remote add origin git@github.com:andaro74/extrasforge.git
git push -u origin main

# 3. repository settings
gh repo edit andaro74/extrasforge \
  --enable-merge-commit --enable-squash-merge=false --enable-rebase-merge=false \
  --delete-branch-on-merge --enable-issues --enable-wiki=false --enable-projects=false \
  --add-topic amazon-bedrock --add-topic bedrock-data-automation --add-topic agentcore \
  --add-topic strands-agents --add-topic knowledge-bases --add-topic mcp \
  --add-topic ai-governance --add-topic hallucination-detection \
  --add-topic media-supply-chain --add-topic aws-cdk

# 4. Actions: default token read-only, fork PRs need approval
gh api -X PUT repos/andaro74/extrasforge/actions/permissions/workflow \
  -f default_workflow_permissions=read -F can_approve_pull_request_reviews=false

# 5. ruleset on main: PR required, no bypass, no force push, merge commits
gh api -X POST repos/andaro74/extrasforge/rulesets --input - << 'JSON'
{
  "name": "main",
  "target": "branch",
  "enforcement": "active",
  "bypass_actors": [],
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    { "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 0,
        "dismiss_stale_reviews_on_push": false,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_review_thread_resolution": false,
        "allowed_merge_methods": ["merge"]
      } }
  ]
}
JSON
# required_status_checks is added to this ruleset at M00 PR 2 (validate,
# cold-review-ruling, regression, cost-cap) and the JSON exported to
# infra/ruleset/main.json in that PR; later gates append there.

# 6. Git LFS confirmation (tracked pattern is in .gitattributes already)
git lfs track   # should list docs/video/**/*.mp4
```

## Before Session A (adoption PR)

- Clone agentkeel beside the repo and check out tag `m00`:
  `git clone git@github.com:andaro74/agentkeel.git ../agentkeel && git -C ../agentkeel checkout m00`
- Confirm `SPEC/00-overview.md` on `main` is the reviewed text; the adoption PR may propose amendments but may not edit it.

## Before Session B (M00 PR 1)

- Run the four fixture clips (one video, one audio, one image, one document; CC-BY; title cards trimmed) through BDA by hand under your own credentials; place the raw outputs at `tests/fixtures/bda/<modality>/raw.json` in the working tree (uncommitted — PR 1 commits them with their README and the NOTICE lines).
- Record for each: source URL, licence line, trim applied, BDA project version, request parameters, run date.
- Copy the model-id table from `../agentkeel/milestones/M00/README.md` at `m00`; it is pasted unchanged into `milestones/M00/README.md`.

## Cost guard at bootstrap

Set an AWS Budgets alarm at $100 on the account used for the by-hand BDA runs before the first fixture is run. agentkeel's own `daily_usd` alarm covers the agent from M06; nothing covers BDA until you set this.
