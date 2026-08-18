# Claude Code Agent Routing

Which model gets which job, how to run background agents on the subscription
you're *not* using, and the safety rules that stop a delegated agent doing
something it shouldn't.

Built and used daily by [Selr AI](https://selrai.com.au). Scrubbed of anything
company-specific so you can adapt it today.

---

## The core idea

Most people run one AI subscription hard and let the other sit idle. Meanwhile
they use their most expensive model to read log files.

Both of those are backwards.

- **Grunt work is the exception that needs justifying**, not delegation.
- **Your top model plans, judges, and reviews.** It should almost never be the
  thing reading 400 files.
- **The provider you're not driving is a free background engine.** Its quota is
  barely touched. Point long-running loops at it.
- **Whoever wrote it never reviews it.** A model grading its own work shares every
  blind spot that produced the work.

---

## What's in it

| File | What it does |
|---|---|
| `rules/model-picking.md` | The 3-axis scoring table, routing rules, and the gotchas of driving a second provider's CLI |
| `rules/delegation-write-safety.md` | Why a "read-only" brief isn't read-only, and the five things every risky delegation must spell out |
| `rules/coding-discipline.md` | Six rules for any code task, in order of how often they get broken |
| `patterns/subagent-briefs.md` | The five parts of a brief another model can actually execute |
| `patterns/cross-model-review.md` | The independent-eye pairing, and how to catch a reviewer that hallucinated its findings |
| `patterns/background-loops.md` | Long autonomous runs: the control surface, the constraints, and when a loop is the wrong tool |

---

## The routing table

Score every model you have access to on three axes, 1–10. **Cost means plan
headroom, not list price**: a model you have effectively unlimited access to
scores high even if its per-token price doesn't.

| axis | means |
|---|---|
| **cost** | how freely you can spend it |
| **intelligence** | how hard a problem it handles unsupervised |
| **taste** | customer-facing copy, layout, teaching material, UI/UX |

Then route:

- **Bulk / mechanical**: migrations, data digging, long documents, clear-spec
  implementation → cheapest high-intelligence model
- **Customer-facing**: proposals, teaching content, UI, copy → taste ≥ 7 only
- **Reviews**: high-taste model, plus a pass from a *different provider*
- **Reasoning effort: high, never above.** Maximum settings cause second-guessing
  loops and roughly double the cost for worse output

When the axes conflict on work that ships: **intelligence ≥ taste ≥ cost.**

Standing permission to escalate: if a cheaper model's output misses the bar, rerun
it on a smarter one without asking. Judge the output, not the price tag.

---

## The safety rule that matters most

> A prohibition stated to you, or stated in a parent brief, **binds every agent in
> the tree: but it does not travel by itself.** You must carry it down, in words,
> every time.

This file exists because a research agent briefed "read-only, documentation only"
spawned a background follow-up that wrote to a live production system. The failure
wasn't the agent. It was the brief.

Design every delegation for the least capable model that might execute it, not
for the one you imagine reading it. Full rule in
[`rules/delegation-write-safety.md`](rules/delegation-write-safety.md).

---

## Install

There's nothing to run. These are instruction files.

```bash
git clone https://github.com/luke-heka/claude-code-agent-routing.git
mkdir -p ~/.claude/rules
cp claude-code-agent-routing/rules/*.md ~/.claude/rules/
```

Then reference them from your `CLAUDE.md` so they load when relevant rather than
sitting in context on every reply. Adjust the model names and scores in
`model-picking.md` to whatever you actually have access to: the table is an
example, not a spec.

---

## Companion repo

This repo is the **delegation** layer.

[**claude-code-operating-system**](https://github.com/luke-heka/claude-code-operating-system)
is the **behaviour** layer: a drop-in `CLAUDE.md` that changes how the AI talks
to you, what it asks about, and how it reports what it did.

---

## Notes

- Model names and scores in the table are illustrative. Rescore for your own plan.
- Nothing here is theoretical. Every rule exists because something went wrong first.

Made by Selr AI.
