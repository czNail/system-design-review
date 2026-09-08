# Module Review Route

Use this reference when the current module is unclear or the discussion is stuck.
Do not dump these questions on the user. Ask at most one that resolves a material uncertainty; proceed without a question when evidence or existing authorization suffices.

## 1. Meaning

Resolve first:
- What is this thing in one sentence?
- Why must it exist as a distinct concept?
- What guarantee does it provide?
- What nearby concern should explicitly not belong here?

If naming it cleanly is difficult, test whether the abstraction mixes semantic entity, physical mechanism, service/process, and protocol adapter.

## 2. Ownership

Identify:
- authoritative state;
- mutable state;
- derived/cache state;
- ownership of decisions, not just data;
- who can change each state and under what authority.

A useful question: if two components disagree, which one wins and why?

## 3. Invariants

Keep the list small. Prefer rules that constrain design, e.g.:
- at most one active writer for a term;
- when visibility promises durability, visible position never exceeds durable position;
- published immutable data never changes;
- metadata never claims durability stronger than underlying data.

If an invariant can only be stated by naming several internal modules, inspect whether ownership is fragmented.

## 4. API

For each public operation, ask:
- Is it a complete domain action?
- Can invalid call ordering be expressed at all?
- Does the caller need to call `validate`, `flush`, `state`, or another helper to complete one logical action?
- Does it expose storage representation or protocol details?
- Should it return a semantic result/snapshot instead?

Prefer APIs that make illegal states or sequences difficult to express.

## 5. Data path

Trace one representative operation end to end:
- allocations/copies;
- encode/decode;
- IPC/network hops;
- locks/queues;
- sync/async boundaries;
- file/object writes;
- cache/materialization.

Do not draw the whole system unless it affects the current boundary.

## 6. Cost

Classify costs:
- required by correctness;
- artifact of current implementation;
- common path;
- rare/recovery path;
- scales with throughput, data size, number of tenants/shards, or concurrency.

Do not call something a bottleneck without evidence. It is valid to call it an architectural cost before measurement.

## 7. Failure

Use `failure-reasoning.md` for persistent/distributed state.
Otherwise ask only what can invalidate the current boundary:
- partial mutation;
- retry/duplicate request;
- stale authority;
- restart;
- reordered visibility.

## 8. Alternative

Generate an alternative only when there is a real decision.
An alternative should move a boundary, ownership rule, or interface—not just rename classes.

State the trade-off in concrete terms:
- which caller knows less/more;
- which invariant gets one owner/multiple owners;
- what cost moves to the hot path;
- which future change gets localized or amplified.
