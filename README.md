# Database System Design Review

An interactive skill for reviewing database and systems architecture **one design decision at a time**.

This skill supports interactive design discussions, with a default traversal from the semantic core outward. It follows a user-specified starting point or discussion order, inspects real code and documents first, and gives a concrete recommendation before moving to the next module. One-shot architecture reports are outside its default trigger.

## What it focuses on

The review is grounded in five system-design lenses:

1. **Semantics** — What is this module, and what guarantee does it provide?
2. **Ownership** — Who owns the authoritative state and design decisions?
3. **Data flow** — How does data move, copy, serialize, persist, and become visible?
4. **Cost** — What does the common path cost in CPU, memory, locks, I/O, network, and coordination?
5. **Failure** — What invariants must still hold after crash, retry, restart, partial persistence, or authority changes?

It also uses software-design principles such as deep modules, information hiding, pulling complexity downward, semantic APIs, and comparing meaningfully different alternatives.

## Interaction model

A normal design discussion looks like this:

```text
inspect real implementation
        ↓
follow the user's starting point, or find the semantic center / authority
        ↓
state the current design
        ↓
give a recommendation
        ↓
resolve one key decision; ask at most one material question if needed
        ↓
record conclusion, rationale, status, and unresolved conditions
        ↓
move to the next dependent module
```

The skill deliberately avoids dumping the entire checklist at once. The unit of progress is a **design decision**, not a document section.
Evidence, prior agreement, or delegated judgment can suffice to proceed without another confirmation. Branch checkpoints preserve decisions visibly without repeating them every turn or creating files by default. User redirection takes priority; unresolved decisions remain recorded as open.

## Repository structure

```text
.
├── README.md
├── SKILL.md
└── references/
    ├── design-principles.md
    ├── module-review.md
    ├── failure-reasoning.md
    └── output-templates.md
```

`SKILL.md` contains the workflow and guardrails. Detailed reasoning guides live under `references/` and are loaded only when relevant.

## Typical use cases

- Review the module boundaries of a database storage engine.
- Design a WAL/log service, page server, cache, recovery subsystem, or distributed metadata component.
- Decide which component should own an invariant or piece of authoritative state.
- Review an API for leaked ordering, validation, locking, or persistence rules.
- Walk through an existing codebase before proposing an architecture refactor.
- Stress-test durability, fencing, recovery, visibility, or retry semantics.

## Core principles

A few rules are intentionally strong:

- Inspect code/docs before asking the user questions that the implementation can answer.
- Follow the user's chosen discussion order; otherwise start from the component that owns the core state and invariants.
- Define visibility, commitment, durability, and response guarantees before selecting failure invariants.
- Split modules by independently changing **knowledge and ownership**, not by file size, function count, or execution phase.
- Prefer complete semantic operations over caller-managed sequences such as `validate → mutate → state`.
- Do not call something a bottleneck without evidence; distinguish architectural cost from measured performance problems.
- Do not accept an existing architecture merely because it already exists.
- Do not manufacture a refactor when the current boundary is already coherent.

## Skill entry point

See [`SKILL.md`](./SKILL.md).
