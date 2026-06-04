---
name: merge-ready-pr-report
description: Final gate for GitHub/GSSoC issue-to-PR work. Use only when preparing the final merge-ready report before commit, push, or PR creation.
---

# Merge Ready PR Report

Use this skill only as a final reporting and verification gate.

The goal is to produce a clear, evidence-backed report showing whether the work is ready for commit, push, or PR creation.

Do not edit files.
Do not stage files.
Do not commit files.
Do not open a PR.
Report only.

## Required Checks

Verify and report:

1. The issue is assigned to `vipul674`.
2. Anti-snipe checks were performed.
3. Duplicate issue and duplicate PR checks were performed.
4. The implementation is scoped to the assigned issue.
5. Protected files were not edited, staged, or committed:
   - `AGENTS.md`
   - `CLAUDE.md`
   - `AI_PR_PLAYBOOK.md`
   - `Memory.md`
6. No `git add .` or `git add -A` was used.
7. Only explicit files are staged.
8. Tests, lint, build, and manual verification were run where relevant.
9. Security findings, if any, follow private disclosure protocol.
10. The PR body includes:
    - Summary
    - Files changed
    - Testing performed
    - Known limitations
    - GSSoC label request
    - AI assistance disclosure
    - `Closes #N`

## Commands To Inspect

Use these commands when available:

```bash
git status --short
git diff --name-only
git diff --cached --name-only
git branch --show-current
git remote -v
```

For diff scope:

```bash
git diff --shortstat upstream/[BASE_BRANCH]...HEAD
```

Fallback:

```bash
git diff --shortstat [BASE_BRANCH]...HEAD
```

For protected file check:

```bash
git status --short -- AGENTS.md CLAUDE.md AI_PR_PLAYBOOK.md Memory.md
git diff --name-only -- AGENTS.md CLAUDE.md AI_PR_PLAYBOOK.md Memory.md
git diff --cached --name-only -- AGENTS.md CLAUDE.md AI_PR_PLAYBOOK.md Memory.md
```

For open PR state check:

```bash
gh api "search/issues?q=type:pr+author:vipul674+is:open&per_page=50" \
  --jq '.items[].html_url' | while read -r pr_url; do
    echo "Checking: $pr_url"
    gh pr view "$pr_url" \
      --json title,url,mergeStateStatus,reviewDecision,statusCheckRollup,isDraft,labels \
      --jq '{
        title: .title,
        url: .url,
        draft: .isDraft,
        mergeState: .mergeStateStatus,
        review: .reviewDecision,
        labels: [.labels[].name],
        checks: [.statusCheckRollup[]? | {name: .name, state: .state}]
      }'
  done
```

## Output Format

Return exactly this structure:

````md
# Merge-Ready PR Report

## Verdict

MERGE_READY or NOT_MERGE_READY

## Issue and Assignment

- Repository:
- Issue:
- Assigned to `vipul674`:
- Assignment verification command/result:

## Anti-Snipe and Duplicate Checks

- Anti-snipe checks:
- Duplicate issue checks:
- Duplicate PR checks:
- Result:

## Scope Control

- Intended scope:
- Files changed:
- Out-of-scope changes avoided:
- Diff shortstat:

## Protected File Check

- `AGENTS.md`:
- `CLAUDE.md`:
- `AI_PR_PLAYBOOK.md`:
- `Memory.md`:

## Staging Check

- Staged files:
- Explicit staging only:
- No `git add .` / `git add -A`:

## Verification

Commands run:

```bash
...
```

Results:

```txt
...
```

Commands not run:

* Command:
* Reason:

## Risks / Known Limitations

*

## PR Body Readiness

* Summary:
* Files changed:
* Testing performed:
* Known limitations:
* GSSoC label request:
* AI assistance disclosure:
* Closes issue:

## Blockers

List blockers if verdict is NOT_MERGE_READY.

If there are no blockers, write:

None.

```
````
