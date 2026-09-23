# ADR-011: Make proto-go progression session-agnostic and single-controller

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-009 established authoritative Progression Context and artifact-driven
continuation between terminating proto-go script invocations and main-agent
continuations.

That model left the session and concurrency consequences implicit.

The product owner has resolved them.

## Decision

A `/go` invocation may originate from any eligible main-agent session.

Different proto-go progressions may be active and advance concurrently.

proto-go must not globally serialize otherwise-independent `/go` progressions.

A logical proto-go progression is not owned by the conversational session that
started or previously advanced it.

A progression may be continued sequentially by another main-agent session.

Conceptually:

```text
session A
→ advances X
→ relinquishes / terminates

session B
→ resolves authoritative Progression Context for X
→ resolves X's managed authoring bindings
→ advances X

session C
→ may later continue X
```

Continuation from another session must preserve the same relevant pre-Admission
progression before Admission, or the same logical proto-go operation and
ManagedContribution after Admission.

A session change must not by itself create a new logical objective,
ManagedContribution, or managed authoring worktree.

Correct continuation must not depend on conversational memory of the previous
session.

## Single-controller rule

At most one independent main-agent procedural controller may actively advance
the same proto-go progression at a time.

Therefore this is permitted:

```text
session A → progression X
session B → progression Y
session C → progression Z
```

concurrently.

This is also permitted:

```text
session A → X → stop
session B → X → stop
session C → X
```

This is not permitted:

```text
session A ─┐
           ├→ independently advance X simultaneously
session B ─┘
```

This rule does not prohibit concurrency internal to one progression when one
authoritative procedural controller causes multiple mechanical, validation,
analysis, or other activities to run concurrently.

This ADR does not select the mechanism enforcing single-controller progression.

## Relationship to existing invariants

`PROTO-GO-INV-002`, `PROTO-GO-INV-011`, `PROTO-GO-INV-040`, and
`PROTO-GO-INV-042` remain normative.

ADR-011 strengthens their session and concurrency consequences; it does not
supersede them.

## Non-decisions

This ADR does not decide:

* lock;
* lease;
* generation;
* compare-and-swap;
* database ownership;
* workflow-engine ownership;
* Turnlock mechanism;
* session-correlation mechanism;
* re-entry lookup mechanism.

Those remain architectural or separately derived decisions.
