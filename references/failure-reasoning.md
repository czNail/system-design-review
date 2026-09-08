# Failure Reasoning

Read this before making correctness claims about persistent or distributed state.

## Establish the contract

Define visibility (to whom), commitment, durability (against which failures), and what a successful response promises. Treat these as distinct properties; establish their required relationships from the system's contract. If the contract is unclear, state the assumption and keep conclusions conditional.

## Transition reasoning order

For one state transition, identify:

1. Preconditions and authority.
2. In-memory mutation.
3. Data persistence.
4. Metadata/control persistence.
5. Publication/visibility to other actors.
6. Crash between each boundary.
7. Duplicate/retry behavior.
8. Restart and re-establishment of authority.

Do not describe only the happy-path recovery procedure. Verify the invariant at each boundary.

## Common invariants

Select only patterns required by the established contract:
- persisted metadata must not claim a state stronger than durable data;
- if visibility promises committed state, consumer-visible state must not exceed the committed boundary;
- if visibility or a successful response promises durability, the exposed or acknowledged state must satisfy that durability boundary;
- stale authority cannot continue mutating state after a newer authority is established;
- retries are idempotent or conflicts are detected explicitly;
- recovery establishes one unambiguous history before normal mutation resumes.

## Failure matrix shortcut

For each critical operation, consider only material cut points:

| Cut point | What is durable? | What is visible? | Safe retry? | Recovery source of truth? |
|---|---|---|---|---|
| before mutation | | | | |
| after data write / before sync | | | | |
| after data durable / before metadata | | | | |
| after metadata / before response | | | | |
| after response / before next operation | | | | |

Only expand the matrix when the current design decision depends on it.
Adapt the cut points to the actual operation ordering and include commitment and response guarantees where relevant; the rows are prompts, not a prescribed persistence protocol.
