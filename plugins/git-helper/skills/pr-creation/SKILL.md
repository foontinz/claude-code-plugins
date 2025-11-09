---
name: git-pr-creation
description: |
  This skill creates pull requests using the GitHub CLI with AI-generated descriptions. It analyzes the current branch, detects available target branches, and generates concise PR descriptions based on the commit history and changes. Use this when asked to create a pull request, file a PR, or when the user uses the `/pr` or `/gpr` command. It automates the PR creation process while ensuring quality descriptions.
---

## Overview

This skill streamlines the pull request creation process by automatically generating professional PR descriptions and handling branch selection through the GitHub CLI. It analyzes commit history and changes to create meaningful, concise descriptions that follow best practices.

## How It Works

1. **Branch Analysis**: The skill identifies the current branch and available target branches using `gh` CLI.
2. **Target Branch Selection**: Presents available branches for user selection.
3. **Change Analysis**: Examines commits and changes between branches.
4. **PR Description Generation**: Creates a concise, well-structured PR description.
5. **PR Creation**: Uses GitHub CLI to create the pull request.

## When to Use This Skill

This skill activates when you need to:
- Create a pull request from current branch.
- Generate a PR description automatically.
- Use the `/pr` or `/gpr` command.
- Streamline the PR creation workflow.

## Examples

### Example 1: Feature Branch PR

User request: "Create a PR for my feature branch"

The skill will:
1. Detect current branch (e.g., `feature/user-auth`)
2. Present target branch options (main, develop, etc.)
3. Analyze commits and changes
4. Generate PR description like:
   ```
   ## Summary
   • Add user authentication module with OAuth2 support
   • Implement login/logout functionality
   • Add user profile management

   ## Test plan
   - [ ] Test OAuth2 login flow
   - [ ] Verify user session management
   - [ ] Test profile update functionality
   ```
5. Create the PR using GitHub CLI

### Example 2: Bug Fix PR

User request: "/gpr"

The skill will:
1. Detect current branch (e.g., `fix/login-validation`)
2. Present target branch options
3. Analyze the bug fix changes
4. Generate focused PR description highlighting the fix
5. Create the PR with appropriate description

## Best Practices

- **Clean Branch History**: Ensure the branch has a clean, logical commit history before PR creation.
- **Descriptive Commits**: Good commit messages help generate better PR descriptions.
- **Review Before Creation**: Always review the generated PR description before creation.
- **Target Branch Selection**: Choose the appropriate target branch (main, develop, etc.).

## Integration

This skill integrates with the GitHub CLI and requires:
- Authenticated GitHub CLI (`gh auth login`)
- Sufficient permissions to create PRs
- Current branch should be pushed to remote

It complements other Git-related skills by providing a complete PR creation workflow.