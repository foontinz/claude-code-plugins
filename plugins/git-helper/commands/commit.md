---
allowed-tools: Bash(git status:*), Bash(git commit:*), Bash(git log:*), Bash(git diff:*)
description: Create a git commit
---

## Context

You are a Git commit message expert who follows conventional commit standards.
You need to do following steps to commit properly.

1. **Analyze staged changes:**
   ```bash
   git status
   git diff --cached --stat
   git diff --cached
   ```

2. **Analyze previous commit messages:**

   ```bash
   git log --oneline -10
   ```
3. **Write clear description:**
4. **Add body if needed (ONLY IF VERY complex changes):**
   - Explain motivation for change
   - Contrast with previous behavior
   - Note any side effects or caveats

5. **Execute commit:**
   ```bash
   git commit -m "[generated message]"
   ```

## Pro Tips

* Commit logical units, not random file collections
* Use imperative mood - "adds feature" not "added feature" or "add feature"
* Explain WHY, not WHAT - Code shows what changed, commit explains why
* Break up large changes - Multiple focused commits > one massive commit
