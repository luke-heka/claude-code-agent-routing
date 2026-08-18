# Subagent briefs: how to write a task another model can actually do

A subagent has no memory of your conversation. It gets exactly the words you send
it and nothing else. Most delegation failures are brief failures.

## The default state

**Agents in flight.** The first move on any substantial task is to split out every
bulk or parallelisable lane and fire subagents at all of them at once, then say
what was delegated.

Bulk context: logs, long documents, transcripts, wide searches, should never
touch your top model. That's what it's for: reading 400 files so the expensive
model reads one summary.

**One task per subagent.** A subagent given three tasks does the first one well
and the other two badly.

## The five parts of a brief

### 1. The outcome, not the process

- ❌ "Look into the auth code and see what you find."
- ✅ "Return every file where a session token is created, with the line number
  and the expiry it sets."

### 2. The exact return shape

Say what you want back. A structured schema if the tool supports it, an explicit
format if not. Otherwise you get prose you have to re-parse.

```
Return a list. One line per finding:
<file>:<line> — <what it does> — <the expiry value>
No commentary, no summary paragraph.
```

### 3. The negative space

What NOT to do, in specific verbs. See `rules/delegation-write-safety.md`, this
is where prohibitions get lost.

### 4. The escape hatch

```
If you find nothing, say so explicitly and state exactly what you inspected.
```

Every brief. No exceptions. Silence is ambiguous; an explicit "I checked X, Y, Z
and found nothing" is a result.

### 5. Enough context to work alone

The subagent cannot see your chat. Paste the relevant constraint, path, or
decision into the brief rather than referring to it.

- ❌ "Following the approach we discussed…"
- ✅ "Using the schema-extension approach (add the field to the config rather than
  refactoring the fetcher)…"

## Orchestrate by altitude

```
Top model      →  plans, judges, synthesises, reviews
Subagents      →  execute mechanical work in parallel
```

The top model should be reading results and making calls. If it's doing the
grunt work itself, the altitude is wrong.

## Fan-out vs pipeline

**Fan-out (barrier)**: all agents run, you wait for all of them, then act on the
whole set. Correct only when the next step genuinely needs every result together:
deduplication, a total count, a comparison across findings.

**Pipeline (no barrier)**: each item flows through all stages independently. Item
A can be in stage 3 while item B is still in stage 1. This is the default. Wall
clock is the slowest single chain, not the sum of the slowest per stage.

If you're about to write "collect all results, transform them, then fan out
again", and the transform has no cross-item dependency: that's a pipeline, and
the barrier is costing you real time.

## Delegated work still ships with tests

No exceptions. Reviewers check that tests exist.
