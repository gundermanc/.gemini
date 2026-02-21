---
name: github
description: Provides instructions and workflows for using the GitHub CLI (gh) to manage pull requests, issues, and project items. Use when the user wants to create PRs, check CI status, respond to feedback, find review requests, or manage work items.
---

# GitHub CLI Integration

## Overview

This skill enables efficient use of the GitHub CLI (`gh`) for common development workflows. It focuses on PR management, issue tracking, and status monitoring.

## Reporting & Formatting

When presenting PRs, issues, or status updates to the user:
- **Concise Table Format**: Prefer a clean Markdown table.
- **Status Emojis**: Use the following mapping in the left-most column to indicate status:
  - ⏳ -- `in progress` / `draft`
  - ‼️ -- `action required` / `changes requested` / `ci failing`
  - ✅ -- `approved` / `ci passing`
  - 🚀 -- `open` / `ready for review`
  - 💤 -- `idle` / `stale`
  - ☑️ -- `merged` / `closed`
  - ❌ -- `fail`

## Common Workflows

### 1. Pull Request Lifecycle
When working on a PR, follow these steps:
- **Creation**: Use `gh pr create --fill` to create a PR from your current branch. Use `--draft` if the work is still in progress.
- **Monitoring**: Use `gh pr status` to see a summary of your PRs and `gh pr checks` to see the results of CI/CD pipelines. When querying PRs directly (e.g. `gh pr list`), prioritize PRs that have changed recently or are open.
- **Reviewing**: Use `gh pr review` to respond to feedback. Use `--approve` to approve or `--comment` with `--body` to add general feedback.
- **Reviews Requested**: Use `gh pr list --search "review-requested:@me"` to identify PRs that need your attention.

### 2. Issue and Work Item Management
- **Status Check**: Use `gh status` for a high-level overview of your work across GitHub.
- **Finding Work**: Use `gh issue list --assignee "@me"` to see what's on your plate.
- **Organization**: Use `gh issue edit` or `gh issue create` to manage task metadata.

### 3. Project Management
For projects tracked in GitHub Projects (v2), use the `gh project` command set.
- `gh project item-list <number> --owner <owner>` to see items in a project.

## References

For a comprehensive list of commands and flags, see [references/gh-commands.md](references/gh-commands.md).

## Usage Guidelines

- **Context Awareness**: Always check if you are in a git repository before running `gh` commands that require one.
- **Truncation and Filtering**: `gh` outputs can be very long. When listing items (e.g., `gh pr list`), use sufficient filters to narrow down to just relevant results so you don't miss anything before the output limit is reached. For example, prioritize items that have changed recently (e.g., using `--search "sort:updated-desc"`) or are open (`--state open`). Always prefer targeted queries over broad ones.
- **Formatting**: Use `--json` flags if you need to process data programmatically, but default to human-readable output for immediate feedback.
- **Drafts**: Encourage the use of draft PRs for work-in-progress to avoid triggering unnecessary notifications or CI runs prematurely.
