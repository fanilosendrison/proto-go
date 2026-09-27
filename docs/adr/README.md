# proto-go Architecture Decision Records

This directory records explicit accepted decisions that establish, clarify, or
amend `proto-go` product semantics or architecture.

The normative Product Intent currently lives in:

```text
../specification/proto-go-spec.md
```

Numbered ADRs are listed in [`index.md`](index.md).

Do not create an ADR merely because an implementation choice is convenient.

Create a semantic ADR only when an actual product-semantic question has been
identified and explicitly resolved by the product owner.

Create an architectural ADR only after the governing Product Intent and derived
invariants are sufficient to constrain that decision.

An unresolved product-semantic question must remain unresolved rather than being
silently decided by implementation.

Accepted ADRs record decision history. The synchronized normative specification
remains the current product-meaning projection.

## Decision history

```text
ADR-020 — Preserve interactive main-agent visibility through /go
— Accepted
— establishes Interactive Main-Agent Visibility across internal execution
  boundaries while preserving ADR-017 control ownership and session-independent
  continuation
```

ADR-020 amends ADR-017 only with respect to the user-facing consequence of
main-agent work crossing execution/control boundaries.
