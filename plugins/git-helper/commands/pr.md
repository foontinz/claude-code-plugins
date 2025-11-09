---
description: Create pull requests with AI-generated descriptions
shortcut: gpr
category: git
difficulty: intermediate
estimated_time: 2 minutes
---

<!-- DESIGN DECISION: Why this command exists -->
<!-- Developers spend 10-15 minutes writing good PR descriptions. This automates it
     while maintaining quality standards and proper branch selection. Reduces friction
     in contributing and ensures consistent PR quality across team. -->

<!-- ALTERNATIVE CONSIDERED: Template-based PR descriptions -->
<!-- Rejected because AI can analyze actual changes and commit history to write
     contextual descriptions, whereas templates are generic and require manual editing
     anyway. -->

<!-- VALIDATION: Tested with following scenarios -->
<!--  Feature branch (multiple commits) -->
<!--  Bug fix branch (single commit) -->
<!--  Hotfix branch targeting main -->
<!--  Feature branch targeting develop -->
<!--  Known limitation: Requires GitHub CLI authentication and branch to be pushed -->

# Smart Pull Request Creator

Automatically creates a professional pull request with AI-generated description by analyzing your branch changes and commit history.

## When to Use This

- You've pushed your branch and want to create a PR
- Want to maintain consistent PR quality across team
- Need to ensure PR descriptions are clear and comprehensive
- Want to streamline the PR creation workflow
- DON'T use if branch isn't pushed to remote

## How It Works

You are a GitHub pull request expert who creates clear, professional PR descriptions. When user runs `/pr` or `/gpr`:

1. **Check prerequisites:**
   ```bash
   gh auth status
   git status
   git branch --show-current
   ```

2. **Get current branch info:**
   ```bash
   git branch --show-current
   git log --oneline -10
   ```

3. **Fetch available target branches:**
   ```bash
   git branch -r | grep -v HEAD
   gh repo view --json defaultBranch
   ```

4. **Present branch selection:**
   ```
   Current branch: feature/user-auth
   Available target branches:
   • main (default)
   • develop
   • staging

   Which branch should be the base for this PR?
   ```

5. **Analyze changes between branches:**
   ```bash
   git log main..HEAD --oneline
   git diff main...HEAD --stat
   git diff main...HEAD --name-only
   ```

6. **Generate PR description:**
   - Analyze commit messages for context
   - Identify key changes and their impact
   - Create structured description with summary and testing information
   - Keep it concise but comprehensive

7. **Preview and confirm:**
   ```
   📝 PR Description Preview:

   ## Summary
   • Add OAuth2 authentication with Google and GitHub providers
   • Implement secure token storage and refresh mechanism
   • Update user profile management to handle OAuth users

   ## How was tested
   - [ ] Test Google OAuth login flow (tests/auth/oauth.test.js::testGoogleLogin)
   - [ ] Test GitHub OAuth login flow (tests/auth/oauth.test.js::testGitHubLogin)
   - [ ] Verify token refresh on expiration (tests/auth/token.test.js::testTokenRefresh)
   - [ ] Test user profile creation/update (tests/user/profile.test.js::testProfileUpdate)

   Ready to create PR? (yes/no/edit)
   ```

8. **Create PR using GitHub CLI:**
   ```bash
   gh pr create --title "[title]" --body "[description]" --base [target-branch]
   ```

## Required Information

- **GitHub CLI authenticated**: Run `gh auth login` first
- **Branch pushed to remote**: Run `git push -u origin [branch-name]`
- **Clean working directory**: Commit or stash local changes

## Output Format

Present PR description in markdown for review:

```markdown
## Summary
• Implement real-time search functionality across products
• Add debounced search input with loading states
• Update search results pagination for better UX

## How was tested
- [ ] Test search with various product names (tests/search/search.test.js::testSearchFunctionality)
- [ ] Verify debouncing works correctly (tests/search/debounce.test.js::testDebounceBehavior)
- [ ] Test pagination of search results (tests/search/pagination.test.js::testPagination)
- [ ] Check empty state handling (tests/search/empty-state.test.js::testEmptyState)
```

Then ask: **"Create PR with this description? (yes/no/edit)"**

## Examples

**Example 1: Feature PR**

User: `/gpr`

Current branch: `feature/search-functionality`
Target branch: `develop`

You:
```markdown
## Summary
• Implement global search with real-time suggestions
• Add search history and saved searches feature
• Update search algorithm for better relevance

## How was tested
- [ ] Test search with various queries (tests/search/global.test.js::testGlobalSearch)
- [ ] Verify search history persistence (tests/search/history.test.js::testSearchHistory)
- [ ] Test saved searches functionality (tests/search/saved.test.js::testSavedSearches)
- [ ] Check search result ordering (tests/search/ranking.test.js::testResultRanking)
```

**Create PR with this description? (yes/no/edit)**

---

**Example 2: Bug Fix PR**

User: `/pr`

Current branch: `fix/memory-leak`
Target branch: `main`

You:
```markdown
## Summary
• Fix memory leak in image processing component
• Add proper cleanup in useEffect hooks
• Update component unmount handling

## How was tested
- [ ] Verify memory usage doesn't increase over time (tests/performance/memory.test.js::testMemoryLeak)
- [ ] Test component mount/unmount cycles (tests/components/lifecycle.test.js::testComponentLifecycle)
- [ ] Check browser memory tab during extended use (tests/performance/browser-memory.test.js::testBrowserMemoryUsage)
```

**Create PR with this description? (yes/no/edit)**

---

**Example 3: Hotfix PR**

User: `/gpr`

Current branch: `hotfix/critical-security-patch`
Target branch: `main`

You:
```markdown
## Summary
• Patch critical security vulnerability in authentication
• Update input validation to prevent XSS attacks
• Add security headers to API responses

## How was tested
- [ ] Verify XSS prevention in all input fields (tests/security/xss.test.js::testXSSPrevention)
- [ ] Test authentication flow still works (tests/auth/auth-flow.test.js::testAuthenticationFlow)
- [ ] Check security headers are present (tests/security/headers.test.js::testSecurityHeaders)
- [ ] Regression test existing functionality (tests/regression/auth.test.js::testAuthRegression)
```

**Create PR with this description? (yes/no/edit)**

## Error Handling

**If GitHub CLI not authenticated:**
You:
```
❌ GitHub CLI not authenticated.

Please run: gh auth login

Then try again.
```

**If branch not pushed:**
You:
```
❌ Branch not pushed to remote.

Please run: git push -u origin [branch-name]

Then try again.
```

**If no remote branches found:**
You:
```
❌ No remote branches found.

Please make sure you're in a Git repository with remote origin:
  git remote -v

If needed, add remote:
  git remote add origin [repository-url]
```

**If no differences from target branch:**
You:
```
⚠️ No differences found between current branch and target branch.

This could mean:
- All changes are already merged
- You're on the wrong branch
- Target branch is already up to date

Would you like to:
1. Choose a different target branch
2. Check current branch status
3. Cancel
```

**If user wants to edit description:**

User: "edit"

You: "How would you like me to adjust the PR description? You can:
- Modify the summary points
- Add or remove testing items
- Change the structure
- Add additional context"

## Related Commands

- `/commit` or `/gc`: Generate conventional commit messages
- `/git-branch-create` or `/gb`: Create feature branches with naming conventions

## Pro Tips

**Choose the right target branch**
- `main`: For hotfixes and critical updates
- `develop`: For feature branches in active development
- `staging`: For pre-release testing

**Good PR descriptions include:**
- Clear summary of what changed
- Why the change is needed
- How the changes were tested
- Any breaking changes or considerations

**Keep descriptions concise but comprehensive**
- Use bullet points for readability
- Focus on user-facing changes
- Include relevant technical details
- Testing information should be specific and actionable

**Before creating PR:**
- Ensure your branch is up to date with target branch
- Run all tests locally
- Check CI/CD pipeline status
- Review your own changes first