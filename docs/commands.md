## Tasks, Queues & Agents

| Command      | Source          | Description                                                                         |
| ------------ | --------------- | ----------------------------------------------------------------------------------- |
| `/queue`     | `queue/`        | Queue one or more prompts for sequential execution after the current task completes |
| `/tasks`     | `tasks/`        | Manage background tasks                                                             |
| `/agents`    | `agents/`       | Manage sub-agents                                                                   |
| `/ultraplan` | `ultraplan.tsx` | Generate a detailed execution plan                                                  |
| `/plan`      | `plan/`         | Enter planning mode                                                                 |

### `/queue`

Queue prompts for Claude Code to execute sequentially using the current session context.

#### Description

The `/queue` command allows users to submit additional prompts while Claude is actively working on another task. Queued prompts are executed automatically in FIFO order once the current task completes.

This enables users to plan multi-step workflows without manually waiting for each step to finish.

#### Examples

Queue a single task:

```text
/queue Add unit tests for the modified files
```

Queue multiple tasks:

```text
/queue Run the test suite
/queue Fix any failing tests
/queue Update the README
/queue Create a changelog entry
```

List queued tasks:

```text
/queue list
```

Remove a queued task:

```text
/queue remove <id>
```

Move a task:

```text
/queue move <id> <position>
```

Clear all queued tasks:

```text
/queue clear
```

Pause queue execution:

```text
/queue pause
```

Resume queue execution:

```text
/queue resume
```

Show queue status:

```text
/queue status
```

#### Example Workflow

```text
> Fix the TypeScript build errors

Claude begins working...

> /queue Add missing test coverage
Queued (#1)

> /queue Run lint and fix issues
Queued (#2)

> /queue Generate release notes
Queued (#3)

Current Queue:
1. Add missing test coverage
2. Run lint and fix issues
3. Generate release notes
```

After the active task finishes, Claude automatically proceeds through the queue while maintaining the same session context.

#### Benefits

* Reduces idle waiting between prompts
* Enables long-running autonomous workflows
* Allows users to batch related development tasks
* Maintains context across sequential executions
* Similar workflow experience to v0 prompt queuing
* Particularly useful for large refactors, code reviews, testing, documentation, and release preparation

#### Potential Implementation

```typescript
interface QueuedPrompt {
  id: string
  prompt: string
  createdAt: number
  status: 'queued' | 'running' | 'completed' | 'failed'
}

interface SessionQueue {
  active?: QueuedPrompt
  pending: QueuedPrompt[]
  history: QueuedPrompt[]
}
```

The queue should be session-scoped and persisted alongside existing session state so queued work survives application restarts and `/resume` operations.
