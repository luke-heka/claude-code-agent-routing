# Coding discipline

Load for any code task. Six rules, in order of how often they get broken.

## 1. Think before coding

State assumptions. Multiple interpretations? Surface them, don't pick silently.
A simpler path exists? Say so. Genuinely unclear? Stop and name it.

## 2. Simplicity first

The minimum code that solves the problem, nothing speculative.

- No abstractions for single-use code
- No unrequested flexibility
- No error handling for impossible cases
- 200 lines that could be 50 → rewrite it

## 3. Surgical changes

Touch only what the request needs.

Don't "improve" adjacent code, comments, or formatting. Match existing style.
Remove only the orphans your change created; never delete pre-existing dead code
unless asked: flag it instead.

## 4. Goal-driven

Turn the task into verifiable success criteria, then loop until they pass.

"Fix the bug" becomes "write a test that reproduces it, then make it pass."

Hard bugs: build a deterministic pass/fail signal **first**, then 3–5 ranked
falsifiable hypotheses, instrument, fix, regression-test.

## 5. Elegance gate

Non-trivial change, before presenting: *"is there a more elegant way?"*

If the fix feels hacky, redo it: *"knowing everything I know now, what's the clean
version?"*

Skip this for simple obvious fixes. Don't over-engineer.

## 6. Prove it before "done"

Never mark complete without demonstrating it works: run it, check the logs, diff
the behaviour against main.

Gut check: **would a staff engineer approve this?**

Going sideways mid-task → stop and re-plan. Don't push through a broken approach.

---

Bias caution over speed. Use judgment on trivial tasks.
