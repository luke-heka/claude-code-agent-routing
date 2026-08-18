# Background loops: mass production on the quota you aren't using

The economics: if you pay for two AI subscriptions and drive one of them 95% of
the time, the other one is an idle engine. A background loop is how you spend it.

## The shape

```
Top model            plans, judges, reviews commits
     │
     ├── loop A  ──►  second provider's CLI, 3h run, own repo/worktree
     ├── loop B  ──►  second provider's CLI, 3h run, own repo/worktree
     └── loop C  ──►  second provider's CLI, 3h run, own repo/worktree
```

The top model's job is reading status and reviewing output. **Never babysitting
iterations.** If you're watching a loop run, you've built it wrong.

## The control surface

Whatever you build, it needs exactly four things:

| Command | Does |
|---|---|
| `start <hours>` | Detached run, writes a PID file, tails to a log |
| `status` | Is it alive, how many iterations, last commit, last heartbeat |
| `roll <n>` | Bounded batch of n iterations, foreground, for testing |
| `stop` | Kill it cleanly |

Plus one file the loop reads and writes every iteration: call it `HANDOFF.md`,
holding the current state, what was just done, and what's next. That file is how
you (or another model, or the same loop after a crash) pick up mid-flight.

## Hard-won constraints

**Lock the success criteria and the verification method BEFORE launch.** A loop
optimising against vague criteria will produce many commits of nothing. Write down
what "done" looks like and how a machine can check it, then start.

**Verify incrementally, not at the end.** Three hours of unverified output is
three hours of work to review, and the failure will be in iteration two.

**Nested CLI invocations often have no network access.** If your loop shells out
to a CLI which shells out again, the inner call may be sandboxed differently.
Test the innermost layer first.

**Run loops on separate repos or git worktrees.** Two loops in one working tree
will fight over the index and both will produce garbage.

**Pause any file-sync or auto-commit daemon before agents edit those files.** A
sync running on a timer will silently revert uncommitted agent edits. Commit
immediately after any change a daemon could clobber.

## When a loop is the wrong tool

- The task has fewer than ~20 iterations of real work → just run agents in parallel
- The success criteria can't be machine-checked → a loop will drift
- Each step depends on a judgment call → that's an interactive session, not a loop

**Parallel agents usually beat a loop.** A loop is for depth over time on one
thread of work. Fan-out is for breadth. Reach for fan-out first.
