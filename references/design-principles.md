# Design Principles

Use this reference when judging whether a module boundary or API is actually better.

## Five-lens system model

For every important component, reason through five models:

1. **Semantic model — What is it?**
   - What capability/guarantee exists because this module exists?
   - What is explicitly outside its responsibility?

2. **Ownership model — Who owns what?**
   - Who is the authority?
   - Who may mutate the state?
   - What is cache, replica, derived state, or observation only?

3. **Data-flow model — How does data move?**
   - Where is data created, copied, serialized, sent, persisted, reconstructed, and consumed?

4. **Cost model — What does it cost?**
   - CPU, memory, locks, cache coherence, syscalls, context switches, I/O, network, copies, write amplification, background work.
   - Separate common-path cost from rare-path cost.

5. **Failure model — Is it still correct after failure?**
   - Crash, partial write, retry, timeout, network partition, concurrent interleaving, restart, recovery.

Short form:

> What is it? Who owns what? How does data move? What does it cost? Is it still correct after failure?

## Complexity as the design cost

Treat complexity as anything that makes a system harder to understand or change:
- **change amplification** — a local change touches many places;
- **cognitive load** — a caller must understand too many rules;
- **unknown unknowns** — hidden dependencies/invariants make safe change unpredictable.

## Deep modules

Prefer modules whose interface is substantially simpler than the complexity they hide.
Ask:
- What can callers safely remain ignorant of?
- Can the implementation change materially without callers changing?
- Does correct use require understanding internal ordering or representation?

Do not use line count or function size as a proxy for module quality.

## Information hiding

Hide **design knowledge**, not merely fields.
Examples:
- page layout;
- writer fencing rules;
- visibility/durability rules;
- crash ordering;
- retry semantics;
- cache policy;
- version/LSN semantics.

If the same rule is recomputed or enforced in several modules, suspect information leakage.

## Pull complexity downward

When complexity is unavoidable, put it in the component that owns the relevant knowledge.
Prefer:
- validation inside the semantic operation;
- safe derivation inside the owner instead of callers recomputing it;
- internal coordination of crash ordering;
- one operation returning the state/result the caller actually needs.

## Split by knowledge, not time

Avoid temporal decomposition: splitting `prepare`, `recover`, `flush`, `cleanup` into modules solely because they occur at different phases.
If those phases share authority and invariants, keeping them together may reduce complexity.

## Design it twice

For important boundaries, compare at least one genuinely different alternative.
Compare:
- interface complexity;
- ownership clarity;
- leaked knowledge;
- change amplification;
- failure reasoning;
- common-path cost;
- extensibility that is actually likely to matter.

Do not prefer an option merely because it has more layers or is more abstract.

## Red flags

Use these as signals, not automatic violations:
- Shallow Module
- Pass-Through Method
- Information Leakage
- Temporal Decomposition
- Conjoined Methods
- Special/General Mixture
- Hard to Name
- Hard to Describe
- Nonobvious Code

The useful question is always: **what complexity does this structure create or hide?**
