# Cross-model review: the independent eye

## The rule

> **Whoever wrote it never reviews it.**

A model reviewing its own output is not a check. It shares every blind spot,
every training bias, and every wrong assumption that produced the code in the
first place. It will confidently approve its own mistakes.

## The pairing

| Who implemented | Who reviews |
|---|---|
| Second-provider CLI | Your primary model |
| Your primary model | Second-provider CLI |
| A subagent | The orchestrating model, plus a cross-provider pass |

Two lanes are worth having:

- **Quick**: a fast sanity pass before calling something done. Minutes.
- **Structured**: a full review against a checklist, for anything that ships or
  touches money, data, or customers.

## What a good review brief contains

1. **The diff or the paths**, not a description of the change.
2. **What the change is supposed to do**, in one sentence.
3. **What you are worried about**: the specific failure you can't rule out.
4. **The escape hatch**: "If you find nothing, say so explicitly and state exactly
   what you inspected."

That last line is not optional. Without it you cannot distinguish "clean" from
"the tool errored and printed nothing".

## Verify the reviewer

**Never pass a delegated review upward unverified.** Read the report, then check
its important claims against the actual code.

Reviewers hallucinate findings. They cite line numbers that don't exist, flag
functions that were deleted three commits ago, and describe behaviour the code
doesn't have. A confident review of a file the reviewer never read looks exactly
like a real one.

Check two or three of the most load-bearing claims. If those hold, the rest is
probably fine. If one doesn't, discard the whole review and rerun it.

## The failure this prevents

The expensive version of this mistake is not a bad review. It's a *clean* review
of code that was never actually examined, which is then trusted, shipped, and
found in production by a customer.
