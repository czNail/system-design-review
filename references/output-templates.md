# Output Templates

Use only after the relevant design branches are resolved or the user explicitly asks for a synthesis.

## Compact module decision

```markdown
### <Module>
- Responsibility:
- Owns:
- Does not own:
- Core invariants:
- Public API:
- Main trade-off:
- Deferred risk:
```

## Architecture synthesis

```markdown
## Module map
<semantic modules and adapters>

## Ownership / authority
<who is source of truth for each critical state>

## Main data path
<one or two representative paths>

## Critical invariants
<small list>

## Cost / hot path
<known architectural costs and measured bottlenecks if any>

## Decisions
<agreed boundaries/APIs>

## Deferred risks
<explicitly postponed questions>
```

## ADR-style decision

```markdown
# Decision: <title>

## Context
<problem and constraints>

## Decision
<chosen boundary/API/model>

## Why
<knowledge/invariant/cost reasoning>

## Alternatives considered
<meaningfully different options>

## Consequences
<what becomes easier/harder>

## Open risks
<intentionally unresolved items>
```
