---
name: gemini-orchestrator
description: "Starts, stops, watches, and lists background Gemini CLI tasks using tmux. Use when the user wants to run or manage a background task."
---

# Gemini Orchestrator

This skill allows Gemini CLI to act as an orchestrator, managing background Gemini CLI tasks using `tmux` and tracking them in a CSV file.

## Core Requirements

- **Task Execution**: All tasks MUST run in a sandbox, with YOLO mode enabled.
  - **Default Mode**: Tasks run in background mode using the `-p` parameter (`gemini -y -s -p "[prompt]"`).
  - **Interactive Mode**: Tasks run interactively using the `-i` parameter (`gemini -y -s -i "[prompt]"`) ONLY if the user explicitly requests an interactive session.
- **Target Resolution**: By default, tasks run locally.
  - **Efficiency**: Minimize the number of extra SSH calls to reduce latency and the frequency of authentication prompts. Combine commands where possible.
- **Environment Setup**: Before creating the first task, check if Gemini is installed and on the path of the target machine (local or remote).
- **Workspace Management**:
  - All code must be stored in `~/code/gemini-orch/[repo-name]/`.
  - The agent must clone the repository if it doesn't already exist.
  - The agent must create a git worktree to perform the work.
- **Task Tracking**:
  - Record each new session in `~/.gemini/tasks.csv`.
  - **Never delete tasks from the CSV file.** They should remain in the file permanently and simply drop to the bottom of any status reports when they are closed.
  - The CSV format must be: `friendly_name,work_tree_path,status,last_updated`
  - `work_tree_path` must be relative to `~`.
  - `last_updated` should be an ISO 8601 timestamp.
  - Valid `status` values: `idle`, `in progress`, `done`, `pr-created`, `pr-issue` (CI failing or comments to address), and `closed` (PR is closed or merged).

## Reporting & Formatting

When presenting task lists or status updates to the user:
- **Concise Table Format**: Prefer a clean Markdown table.
- **Priority Ordering**: Sort primarily by "needs attention" status:
  1. `in progress` / `pr-issue`
  2. `idle` / `done` / `pr-created`
  3. `closed`
- **Recency Ordering**: Secondary sort by `last_updated` (newest to oldest).
- **Status Emojis**: Use the following mapping in the left-most column:
  - ⏳ -- `in progress`
  - ‼️ -- `pr-issue`
  - ✅ -- `done` / `pass`
  - 🚀 -- `pr-created`
  - 💤 -- `idle`
  - ☑️ -- `closed`
  - ❌ -- `fail` (if applicable)

## Workflows

Always generate a unique, identifiable friendly name (session name) for each task, such as `gemini_task_<timestamp>` or `gemini_task_<short-description>`.

### 1. Start Task

Starts a new Gemini CLI instance in a detached tmux session. Records the task in `~/.gemini/tasks.csv` with the appropriate status and timestamp.

**Default (Background Mode):**
```bash
echo "<session_name>,<relative_worktree_path>,in progress,$(date -u +%Y-%m-%dT%H:%M:%SZ)" >> ~/.gemini/tasks.csv
tmux new-session -d -s <session_name> "gemini -y -s -p '<prompt>'"
```

**Interactive Mode (If requested):**
```bash
echo "<session_name>,<relative_worktree_path>,in progress,$(date -u +%Y-%m-%dT%H:%M:%SZ)" >> ~/.gemini/tasks.csv
tmux new-session -d -s <session_name> "gemini -y -s -i '<prompt>'"
```

### 2. Watch Task (Attach)

Attaches to an existing tmux session to give the user interactive control.

**CRITICAL**: *Before* running the watch command, you MUST inform the user: "Please press `Tab` to focus into the session once it starts."

```bash
tmux attach-session -t <session_name>
```

### 3. Stop Task (Kill)

Kills the specified tmux session, terminating the background Gemini CLI task. Update the status in `tasks.csv` as needed.

```bash
tmux kill-session -t <session_name>
```

### 4. List Tasks

When the user asks to "list", query the task status, format it into the required Markdown table, and output it to the user.

1.  Read the tasks and sessions:
    ```bash
    cat ~/.gemini/tasks.csv
    tmux ls | grep gemini_task
    ```
2.  Render the updated Markdown table.

### 5. Revive Session

Revives an old session if the tmux session has ended. Uses the `--resume` parameter to continue the session state.

```bash
tmux new-session -d -s <session_name> "gemini --resume <session_name> -p '<prompt>'"
```
*(Note: Use `-i` instead of `-p` if resuming interactively).*

### 6. Change Session Type

If the user asks to change the session type (e.g., background to interactive, or vice versa), stop the current tmux session and start Gemini again with `--resume` and the new mode flag.

```bash
tmux kill-session -t <session_name>
tmux new-session -d -s <session_name> "gemini --resume <session_name> <new_mode_flag> '<prompt>'"
```

### 7. Edit Workflow

Opens VS Code in the git worktree directory associated with the task.

```bash
code ~/<relative_worktree_path>
```