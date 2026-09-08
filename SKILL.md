---
name: database-system-design-review
description: >-
  Interactive architecture/design review for database and systems modules. Use when the user wants
  to reason through a real design interactively, discuss module boundaries or APIs step by step,
  or says things like "一个模块一个模块讨论", "带我一步步设计", or "walk through this architecture with me".
  Use when the user wants a design dialogue to resolve decisions and trade-offs. Do not use for
  one-shot architecture reports, ordinary bug fixing, implementation, or line-by-line code review
  unless the user asks to work through architecture decisions interactively.
---

# Database / System Design Review

Help the user reach a shared, evidence-backed design one decision at a time.

## Core contract

This is a **design dialogue**, not a one-shot design report.

- Work on **one module or one design decision at a time**.
- Give your own recommended answer; do not merely interview the user.
- Keep each turn compact enough to discuss.
- Do not move on until the current decision is **agreed, consciously deferred, or shown to be unnecessary**.
- Existing agreement or authorization to use your judgment is sufficient to proceed; do not request repeated confirmation. If the user redirects the discussion, follow their chosen topic and retain any unresolved decision as open.
- Do not dump the full checklist or review several modules at once unless the user explicitly asks for a synthesis.
- Challenge weak boundaries and assumptions directly, but explain why.

The default unit of progress is a **decision**, not a section of a document.

## Do your homework first

If code, design docs, diagrams, prior decisions, or repository access exist, inspect them before asking questions they can answer.

Use evidence to establish:
- current module/process boundaries;
- authoritative state and ownership;
- public APIs and callers;
- main data path;
- persistence and concurrency boundaries.

Do not ask the user to explain facts the implementation already reveals.
If implementation and the user's mental model disagree, surface the mismatch explicitly.

## Start at the semantic center

Do not automatically start from `main`, networking, file layout, or the user's proposed module split.

Find the component that owns the core state/invariants. A useful test is:

> If this state becomes wrong, which component is responsible for making the system correct again?

Use this as the default starting point when the user has not chosen one. When the user specifies a module or discussion order, follow it and inspect only the dependencies needed to assess that decision. In the default traversal, clarify core responsibility and ownership before moving outward.

For the underlying design philosophy, read `references/design-principles.md` when you need to evaluate a boundary or compare alternatives.

## Conversation loop

For the current module/decision, repeat this loop:

1. **Establish** — state the observed design in 1–3 sentences, grounded in code/docs when available.
2. **Recommend** — give the design you currently prefer and the main reason.
3. **Decide** — ask at most **one high-value question**, only when unresolved information or a user choice materially affects the decision. When evidence, prior agreement, or delegated judgment suffices, state the conclusion and continue without asking for confirmation. Do not label your own recommendation as user agreement.
4. **Record** — maintain a compact, visible decision record at branch checkpoints: conclusion, rationale, status (`AGREED`, `DEFERRED`, or `OPEN`), and any unresolved conditions. Identify decisions made under delegated judgment. Do not repeat the record every turn or write a file unless requested; do not reopen an agreed decision without new evidence.
5. **Continue** — stay on the same branch if unresolved unless the user redirects; otherwise choose the next dependency/module.

A normal turn should usually look like:

> **Current conclusion:** ...  
> **Why:** ...  
> **I recommend:** ...  
> **One thing to decide:** ...

Do not mechanically print these labels if natural prose is clearer.
Omit the question when no material input is needed.

## What to resolve for a module

Do not ask all of these at once. Use them as a dependency order:

1. **Meaning** — What is this module, and what guarantee does it provide?
2. **Ownership** — What state/design knowledge does it own? What must it not own?
3. **Invariants** — Which few rules define correctness?
4. **API** — What complete semantic operations should callers see?
5. **Data path** — How does one representative operation move through the system?
6. **Cost** — What is inherently expensive or serialized on the common path?
7. **Failure** — What must still hold after partial failure, retry, restart, or concurrent authority changes?
8. **Alternative** — Is there a meaningfully different boundary/API worth comparing?

Skip steps that do not affect the current decision.
For a detailed module interrogation route, read `references/module-review.md`.
For durable/distributed state, read `references/failure-reasoning.md` before claiming correctness.

## Boundary rule

Split modules by **independently changing knowledge and ownership**, not by:
- file size;
- function count;
- execution phase (`PrepareManager`, `RecoveryManager`, `FlushManager`);
- a desire for more layers.

Keep related operations together when they share the same authority, state, and invariants.
Split when two parts can change independently without needing to understand each other's rules.

## API rule

Prefer complete semantic operations that hide validation, ordering, locking, and derived state.

Prefer:

```text
append_wal(token, start_lsn, data) -> AppendResult
```

over caller-managed sequences such as:

```text
validate_writer(token)
append(start_lsn, data)
state()
```

If callers must remember a hidden sequence to stay correct, complexity has leaked upward.

## Scope discipline

Stay in the design lane the user is discussing.

- If the question can be resolved from code, inspect code instead of asking.
- If a performance concern appears, distinguish an architectural cost from an unmeasured bottleneck.
- If a correctness claim depends on protocol semantics that are not established, state the assumption instead of declaring a bug.
- If implementation work becomes the next step, finish the design decision first; only implement if the user asks.

## Moving on

At the end of a branch, keep the checkpoint short:

- **AGREED** — clear decision and rationale; note whether accepted by the user or decided under delegated judgment.
- **DEFERRED** — explicitly out of scope or intentionally postponed; name the remaining risk.
- **OPEN** — material uncertainty remains; stay here unless the user chooses another topic, in which case retain the unresolved conditions for later.

When a branch is resolved, follow the user's chosen order. Otherwise select the next module based on dependency, normally moving from semantic core toward adapters, protocols, storage mechanics, and process/runtime plumbing.

## Synthesis

Only when the important branches are resolved, or the user asks to summarize, synthesize:
- module map;
- ownership/authority map;
- public API decisions;
- critical invariants;
- main data path and hot path;
- deferred risks;
- agreed refactors or ADR decisions.

For reusable final formats, read `references/output-templates.md`.

## Guardrails

- Do not accept the user's proposed architecture merely because it is already implemented.
- Do not manufacture a problem to justify a refactor.
- Do not equate more abstraction with better design.
- Do not hide trade-offs behind words such as "cleaner", "decoupled", or "scalable"; say what knowledge, cost, or invariant actually moves.
- Do not ask five questions in one turn.
- Do not repeat resolved context unless new evidence changes the decision.
