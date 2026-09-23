# ADR-012: Load /go procedural instructions incrementally

- Status: Accepted
- Date: 2026-09-23

## Context

The `/go` skill is the procedural authority governing the main agent.

Requiring the main agent to load the complete proto-go procedural instruction
corpus before progression begins would contradict the state-driven,
artifact-driven model established by ADR-008 and ADR-009.

The product owner has resolved the instruction-loading model.

## Decision

The `/go` skill remains the procedural authority governing the main agent.

A `/go` invocation must not require the main agent to load the complete
proto-go procedural instruction corpus before progression begins.

The main agent must be able to begin from a bounded bootstrap instruction set
sufficient to enter the proto-go progression correctly.

Additional authoritative procedural instructions are resolved and loaded only
when they become applicable to the current progression state or authorized
continuation.

Instruction material that is unrelated to the current continuation must not be
required merely because it may become relevant later.

Conceptually:

```text
/go
↓
bounded bootstrap instructions
↓
resolve sufficient Progression Context
↓
current mechanical progression
↓
Continuation Artifact
↓
Continuation Policy determines applicable continuation
↓
resolve/load only currently applicable procedural instructions
↓
main-agent continuation
↓
fresh script invocation as applicable
↺
```

## Progression Context versus procedural instructions

The two are distinct:

```text
Progression Context
=
authoritative information about the progression and what is true
```

```text
procedural instructions
=
authoritative procedure the main agent needs for the currently applicable
continuation
```

Incremental instruction loading must not transfer procedural authority to:

```text
proto-go script
Continuation Artifact
```

The script establishes facts and continuation conditions.

The `/go` skill and its Continuation Policy remain authoritative for determining
which procedural behavior is applicable.

## Non-decisions

This ADR does not decide:

* instruction storage;
* instruction packaging;
* instruction discovery;
* instruction transport;
* instruction file layout;
* retrieval engine;
* caching implementation.

Those remain architectural or separately derived decisions.
