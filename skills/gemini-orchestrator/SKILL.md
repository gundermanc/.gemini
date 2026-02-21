---
name: gemini-orchestrator
description: "Starts, stops, watches, and lists background Gemini CLI tasks using tmux. Use when the user wants to run or manage a background task."
---

# Gemini Orchestrator

This skill allows Gemini CLI to act as an orchestrator, managing background Gemini CLI tasks using `tmux`.

## Core Requirements

- **Task Execution**: All tasks MUST run in a sandbox, with YOLO mode enabled, in interactive mode: `gemini -y -s -i "[prompt]"`
- **Target Resolution**: By default, tasks run locally.
  - **Efficiency**: Minimize the number of extra SSH calls to reduce latency and the frequency of authentication prompts. Combine commands where possible.
- **Environment Setup**: Before creating the first task, check if Gemini is installed and on the path of the target machine (local or remote).
  - **Path Discovery**: Check `~/.bashrc` on the target machine for the path to `gemini` and `node`.
- **Workspace Management**:
  - All code must be stored in `~/code/gemini-orch/[repo-name]/`.
  - The agent must clone the repository if it doesn't already exist.
  - The agent must create a git worktree to perform the work.

## Workflows

Always generate a unique, identifiable session name for each task, such as `gemini_task_<timestamp>` or `gemini_task_<short-description>`.

### 1. Start Task

Starts a new Gemini CLI instance in a detached tmux session.

```bash
tmux new-session -d -s <session_name> "gemini -y -s -i '<prompt>'"
```

### 2. Watch Task (Attach)

Attaches to an existing tmux session to give the user interactive control.

**CRITICAL**: *Before* running the watch command, you MUST inform the user: "Please press `ctrl + f` to focus into the session once it starts."

```bash
tmux attach-session -t <session_name>
```

### 3. Stop Task (Kill)

Kills the specified tmux session, terminating the background Gemini CLI task.

```bash
tmux kill-session -t <session_name>
```

### 4. List Tasks

Lists all running tmux sessions that match the `gemini_task` prefix.

```bash
tmux ls | grep gemini_task
```
