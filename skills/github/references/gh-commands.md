# GitHub CLI (gh) Reference

This reference provides common `gh` commands for typical developer workflows.

## Pull Requests

### Creating a Pull Request
- Create a PR from the current branch: `gh pr create --fill`
- Create a draft PR: `gh pr create --draft --fill`
- Create with specific title and body: `gh pr create --title "My Title" --body "My description"`

### Checking PR Status
- Check status of the current branch's PR: `gh pr status`
- View CI check results: `gh pr checks`
- List PRs assigned to you: `gh pr list --assignee "@me"`

### Reviewing and Responding
- List PRs where your review is requested: `gh pr list --search "review-requested:@me"`
- Add a comment to a PR: `gh pr comment <number> --body "My comment"`
- Approve a PR: `gh pr review <number> --approve`
- Request changes on a PR: `gh pr review <number> --request-changes --body "Reasoning"`
- Submit a general review comment: `gh pr review <number> --comment --body "Feedback"`

## Issues and Work Items

### Querying Issues
- List open issues in the current repo: `gh issue list`
- List issues assigned to you: `gh issue list --assignee "@me"`
- View a specific issue: `gh issue view <number>`

### Organizing Work
- Create an issue: `gh issue create --title "Bug report" --body "Steps to reproduce"`
- Add a label to an issue: `gh issue edit <number> --add-label "bug"`

## Projects (GitHub Projects V2)

- List items in a project: `gh project item-list <project-number> --owner <owner>`
- Edit project items: `gh project item-edit`

## General Status
- Summary of relevant PRs, issues, and notifications: `gh status`
