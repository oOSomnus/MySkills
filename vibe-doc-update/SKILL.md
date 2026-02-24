---
name: vibe-doc-update
description: Update project documentation based on currently staged git changes, including CLAUDE.md, README, and other relevant docs, then stage doc edits. Use when user asks to "vibe doc update", "update docs from staged changes", "同步文档", "根据暂存改动更新文档", or "更新CLAUDE.md和README".
metadata:
  author: oosomnus
  version: 1.0.0
  category: documentation
  tags:
    - docs
    - readme
    - claude-md
    - staged-diff
---

# Vibe Doc Update

## Purpose
Synchronize docs with the current staged code changes before commit.

Primary targets:
1. `CLAUDE.md`
2. `README.md` (or `readme.md`)
3. Other relevant project docs

## Workflow

### Step 1: Validate staged input
Run:
```bash
git rev-parse --is-inside-work-tree
git diff --staged --name-status
```

If nothing is staged, stop and ask user to stage changes first.

### Step 2: Analyze staged impact
Read staged patch:
```bash
git diff --staged
```

Identify doc-impact categories:
1. Setup or environment changes
2. CLI/API/interface changes
3. Behavior or workflow changes
4. Config or policy changes
5. Added/removed features

### Step 3: Locate docs to update
Check for core docs:
```bash
ls CLAUDE.md README.md readme.md
```

Find additional candidate docs:
```bash
rg --files -g '*.md'
```

Prioritize:
1. Docs related to modified modules
2. Docs referenced by README or CLAUDE.md
3. Any runbook, guide, or reference that now becomes outdated

### Step 4: Apply documentation updates
Editing rules:
1. Update only content implied by staged changes.
2. Keep wording concrete and testable.
3. Preserve existing style and structure.
4. Do not invent unimplemented behavior.
5. If staged changes do not affect docs, report "no doc impact" clearly.

Minimum expectations:
1. `CLAUDE.md`: update workflow, commands, guardrails, or agent behavior if affected.
2. `README`: update features, usage, setup, examples, or compatibility if affected.
3. Related docs: update detailed procedures and references.

### Step 5: Stage documentation edits
After edits:
```bash
git add -A -- CLAUDE.md README.md readme.md
git add -A -- docs
git add -A -- '*.md'
```

Then verify:
```bash
git diff --staged --name-status
```

### Step 6: Report coverage
Provide:
1. Which docs were updated
2. What changed in each doc
3. Remaining doc gaps, if any

## Examples

### Example 1
User says: `vibe doc update`

Action:
1. Inspect staged code changes.
2. Update CLAUDE.md, README, and related docs.
3. Stage doc updates.
4. Return coverage summary.

### Example 2
User says: `根据当前暂存内容同步文档`

Action:
1. Analyze staged patch.
2. Apply Chinese or existing project language style.
3. Stage updated markdown files.
4. Summarize completed updates.

## Troubleshooting

### Error: core doc file missing
Cause: repository has no `CLAUDE.md` or no README.
Solution:
1. Update existing docs only.
2. Report missing expected files.
3. Ask user whether to create missing docs.

### Error: doc scope unclear
Cause: staged changes are broad or mixed.
Solution:
1. Group changes by feature/module.
2. Map each group to explicit doc sections.
3. Ask targeted clarification only if ambiguity blocks accurate updates.

### Error: staged doc conflicts
Cause: docs already staged with different intent.
Solution:
1. Respect user-staged doc intent.
2. Append focused updates instead of rewriting unrelated sections.
3. Show final staged doc list for confirmation.
