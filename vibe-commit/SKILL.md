---
name: vibe-commit
description: Generate a high-signal commit message from currently staged git changes and create the commit. Use when the user asks to "vibe commit", "commit staged changes", "write commit message and commit", "生成提交信息并提交", or "提交暂存内容". Commit message language follows explicit user request; default is English.
metadata:
  author: oosomnus
  version: 1.0.0
  category: git
  tags:
    - git
    - commit
    - staged-diff
---

# Vibe Commit

## Purpose
Create a clean commit from the current staged changes only:
1. Inspect staged diff.
2. Generate the commit message in requested language (default English).
3. Run `git commit`.

## Workflow

### Step 1: Validate state
Run:
```bash
git rev-parse --is-inside-work-tree
git diff --staged --name-status
```

If no staged files exist, stop and tell the user to stage files first.

### Step 2: Detect preferred language
Use explicit user preference if provided, for example:
- "中文"
- "English"
- "Japanese"

If no language is specified, use English.

### Step 3: Draft commit message
Use staged diff as the only source of truth:
```bash
git diff --staged
```

Message rules:
1. Prefer Conventional Commit format: `type(scope): summary`
2. Keep summary specific and outcome-focused.
3. Keep subject concise (around 50-72 chars when possible).
4. Add body bullets only when they add real value:
   - key behavior change
   - migration or breaking change
   - important side effects
5. Do not mention unstaged or unrelated work.

Type guidance:
- `feat`: user-visible functionality
- `fix`: bug fix
- `refactor`: structural code change without behavior change
- `docs`: documentation-only change
- `test`: tests only
- `chore`: maintenance/config/build

### Step 4: Commit
Default behavior is to commit immediately after drafting message.

Use:
```bash
git commit -m "type(scope): summary"
```

For multi-line messages:
```bash
git commit -m "type(scope): summary" -m "- bullet 1
- bullet 2"
```

If the user asks for preview only, show message draft and do not commit.

### Step 5: Report result
After committing, show:
1. Commit hash
2. Final commit subject
3. Files included

Use:
```bash
git show --name-only --oneline -n 1
```

## Examples

### Example 1
User says: `vibe commit`

Action:
1. Read staged diff.
2. Generate English commit message.
3. Commit and report hash.

### Example 2
User says: `用中文提交，vibe commit`

Action:
1. Read staged diff.
2. Generate Chinese commit message.
3. Commit and report hash.

## Troubleshooting

### Error: no staged changes
Cause: files not added to index.
Solution:
1. Run `git status --short`
2. Stage files with `git add ...`
3. Re-run workflow

### Error: commit hook failed
Cause: lint/test/format hooks rejected commit.
Solution:
1. Read hook output.
2. Fix issues.
3. Re-stage updated files.
4. Re-run commit.

### Error: uncertain commit scope
Cause: staged changes mix unrelated topics.
Solution:
1. Recommend splitting into multiple commits.
2. Ask user whether to continue with one combined commit.
