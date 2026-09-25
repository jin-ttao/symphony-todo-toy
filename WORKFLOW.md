---
tracker:
  kind: github
  provider:
    repo: "jin-ttao/symphony-todo-toy"
    token: "$GITHUB_TOKEN"
  required_labels: ["symphony"]
  active_states: ["open"]
  terminal_states: ["closed"]
polling:
  interval_ms: 5000
workspace:
  root: /private/tmp/symphony-todo-workspaces
hooks:
  after_create: |
    git clone https://github.com/jin-ttao/symphony-todo-toy.git .
agent:
  max_concurrent_agents: 1
  max_turns: 3
codex:
  command: codex app-server
  approval_policy: "never"
  thread_sandbox: workspace-write
  turn_sandbox_policy:
    type: workspaceWrite
    networkAccess: true
  read_timeout_ms: 60000
  turn_timeout_ms: 600000
  stall_timeout_ms: 600000
observability:
  dashboard_enabled: true
---

You are implementing GitHub issue {{ issue.identifier }} in an isolated Symphony workspace.

Issue title: {{ issue.title }}
Issue body: {{ issue.description }}

Complete this one issue end to end:

1. Inspect the repository and issue. Keep the implementation minimal and touch only what the issue requires.
2. Create branch `codex/gh-{{ issue.id }}-button-style` from `main`.
3. Implement and verify the change locally. Do not add dependencies, CI, infrastructure, or abstractions.
4. Commit with a concise message and push the branch to `origin`.
5. Use the `github_api` tool to POST `/repos/jin-ttao/symphony-todo-toy/pulls` with base `main`, head `codex/gh-{{ issue.id }}-button-style`, a concise title, and a body that includes `Closes #{{ issue.id }}` plus verification performed.
6. Use `github_api` to add one issue comment containing the created PR URL.
7. Use `github_api` to DELETE `/repos/jin-ttao/symphony-todo-toy/issues/{{ issue.id }}/labels/symphony` so Symphony will not run the issue again.

Do not merge the PR. Do not close the issue manually. Stop only after the open PR exists and the `symphony` label is removed.
