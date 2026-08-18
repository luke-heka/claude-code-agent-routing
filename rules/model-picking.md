# Model picking: which model gets which job

## The scoring table

Score each model you have access to on three axes, 1–10. **Cost is scored as plan
headroom, not list price**: a model you have effectively unlimited access to
scores 9 even if its per-token price is high.

- **Cost**: how freely you can spend it. Higher = cheaper to you.
- **Intelligence**: how hard a problem it handles unsupervised.
- **Taste**: customer-facing copy, layout, teaching material, UI/UX, workflow design.

Example table (yours will differ: rescore it when your plan limits change):

| model | cost | intelligence | taste |
|---|---|---|---|
| frontier model A (via second provider CLI) | 9 | 9 | 5 |
| frontier model A, previous version | 9 | 8 | 5 |
| mid-tier model, primary provider | 5 | 5 | 7 |
| large model, primary provider | 4 | 7 | 8 |
| top model, primary provider | 2 | 9 | 9 |

The insight the table encodes: **your second provider's quota is usually barely
touched.** If you pay for two AI subscriptions and drive one of them 95% of the
time, the other one is a free background engine.

## Routing rules

- **These are defaults, not limits.** Standing permission to override: if a
  cheaper model's output misses the bar, rerun on a smarter one without asking.
  Judge the output, not the price tag.
- **Use cheap models to gather information first.** Never let cost pick the model
  for anything that ships.
- When the axes conflict on shipped work: **intelligence ≥ taste ≥ cost.**
- **Bulk / mechanical work**: clear-spec implementation, data analysis,
  migrations, log and transcript digging, long PDF reads, bulk data pulls → the
  cheapest high-intelligence model you have.
- **Anything customer-facing**: proposals, teaching content, UI, marketing copy →
  taste ≥ 7 only.
- **Reviews of plans and implementations** → a high-taste model, PLUS a pass from
  a *different provider* as an independent perspective.
- **Reasoning effort: high, never above.** Maximum-effort settings cause
  second-guessing loops, bloated output, and roughly double the cost. Effort
  raises thinking per step, not the number of steps.
- **Delegated implementation still ships with tests.** No exceptions. Reviewers
  check that tests exist.

## The hard constraint

> **Whoever wrote it never reviews it.**

If model A implements, model A's review is not an independent check, it's the
same distribution grading its own homework. Cross-provider review is the only
review that catches a whole class of shared blind spots.

## Prompting a second-provider CLI

Much shorter and simpler than prompting a subagent on your primary provider. A
CLI tool invoked with an explicit task doesn't take unsolicited action, so
guardrail language is mostly noise. One tight, self-contained task statement.

**Every such prompt must include:**

```
If you find nothing, say so explicitly and state exactly what you inspected.
```

Without this, silence gets misread as a clean result and the whole run gets
repeated.

## Operational gotchas

Collected the hard way. Yours will differ, but the *shape* of these will not.

- **Long runs exceed default shell timeouts.** Use a generous timeout or run in
  the background for anything over ~2 minutes.
- **Outside a git repo, some CLIs refuse to run.** Find the skip flag before you
  need it.
- **A CLI with an expired auth token on any configured integration can die
  silently** with an unhelpful error. For pure-code tasks, run it with
  integrations disabled.
- **Verify before presenting.** Read the delegated report and check its important
  claims against the actual code before relaying them upward. Never pass a
  delegated review through unverified.

## Using a second provider inside your primary provider's subagents

Most subagent APIs only accept their own vendor's models. The wrapper pattern:

1. Spawn a thin, cheap subagent on your primary provider.
2. Its prompt tells it to: write a short self-contained prompt, shell out to the
   other provider's CLI, and return the results faithfully with minimal commentary.
3. Prefix all wrapper output with a marker like `[external/<model>]` so it's
   visible which subagents are running on the other provider.
4. If the external call times out, the wrapper retries once before reporting failure.

## Maintenance

When a delegation goes wrong, don't rebuild the system. Describe the failure,
get a one-to-two-line fix, and append it here. Keep this file under ~80 lines of
actual rules; prune anything that stops earning its place.
