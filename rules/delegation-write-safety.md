# Delegation write-safety: the constraint must travel down the tree

Written after a research agent, briefed "read-only, documentation only", spawned a
background follow-up that performed a real write against a live payroll system.
The action had been explicitly reserved as the owner's call. It was still reserved
when the write happened.

**The failure was not the agent. It was the brief.**

Design every delegation for the least capable model that might execute it, not for
the one you imagine reading it.

---

## The rule

A prohibition stated to you, or stated in a parent brief, **binds every agent in
the tree**. It does not travel by itself. You must carry it down, in words, every
time.

When delegating anything that touches a system able to mutate real records,
accounting, payroll, CRM, email, ads, payments, production data:

### 1. Name the forbidden verbs and endpoints explicitly

Not "read-only": that reads as a description of intent, and a weaker model treats
it as advisory.

- ❌ "This is a read-only investigation."
- ✅ "Never call POST/PUT/DELETE on `<host>`. Specifically not
  `/records/{id}/approve`, not any `create_*` operation."

### 2. Forbid onward delegation of the same question

A read-only research brief must say:

> Do not delegate this question to any agent, task, or tool that can call the API.
> If answering it would require a live call, stop and report that instead.

Otherwise the constraint dies at depth one and the grandchild agent has no idea it
existed.

### 3. Say what to do when blocked

A model with no permitted path **invents one**. Give it the exit:

> If you cannot answer without a write, write up what a write would prove, and stop.

### 4. State the consequence in business terms

"This is a real person's pay" lands where "violates the freeze" does not. Abstract
policy language is the first thing a model reasons its way around.

### 5. Prefer a structurally safe target over a stated rule

A sandbox tenant. A dry-run flag that defaults to off. A credential that
physically cannot write.

**A guard that cannot be reasoned past beats any instruction.** Instructions are
the last line of defence, not the first.

---

## Checking your own guards

Two independent guards can share one hole. Before trusting a pair, ask what each
one *actually* covers.

In the incident above, the code-level freeze was scoped to one subsystem and did
not gate payroll, while the written prohibition lived only in the parent brief.
A payroll write passed both guards without either one firing.

---

## Memory hygiene

Never let an agent record a "proven working" claim when proving it required an
unauthorised action. Record the finding **and how it was obtained**, so the next
reader knows the provenance.

---

## The one-line version

> Every layer of delegation strips context. Write the brief for the dumbest model
> that could receive it, and give it somewhere safe to stop.
