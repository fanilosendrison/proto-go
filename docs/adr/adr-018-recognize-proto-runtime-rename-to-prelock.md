# ADR-018: Recognize proto-runtime rename to Prelock

- Status: Accepted
- Date: 2026-09-25

## Decision

1. The reusable execution substrate previously named `proto-runtime` is now
   named `Prelock`.
2. This is a product/repository rename only.
3. ADR-017's architectural allocation is unchanged.
4. Proto-go continues to own domain semantics and workflow progression
   decisions.
5. Prelock continues to own the generic execution substrate assigned to the
   former `proto-runtime` by ADR-017.
6. `PROTO-GO-INV-059` retains the same invariant identity.
7. Only the referenced product name inside `PROTO-GO-INV-059` changes from
   `proto-runtime` to `Prelock`.
8. No proto-go Product Intent change is introduced.
9. No proto-go architecture change is introduced.
10. Historical ADR-017 prose may retain `proto-runtime` when describing the
    original decision chronologically.
11. Current proto-go documentation must use `Prelock`.
