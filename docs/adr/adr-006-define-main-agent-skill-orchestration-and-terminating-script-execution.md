# ADR-006: Define main-agent skill orchestration and terminating script execution

- Status: Accepted
- Date: 2026-09-23

## Context

The existing Product Intent defines proto-go's managed
implementation-to-publication semantics but intentionally deferred much of its
execution model.

`PROTO-GO-INV-012` consequently asserted that proto-go product semantics were
execution-engine independent.

That assertion is no longer correct.

The product owner has clarified that proto-go is intentionally defined around a
specific interaction model:

- the user is operating in a main-agent session;
- the user invokes `/go` as part of an implementation request;
- the `/go` skill provides the procedure followed by the main agent;
- the first procedural step invokes the proto-go script;
- that script runs to completion and returns outputs;
- only then does the main agent continue the proto-go procedure;
- the script cannot suspend into a main-agent continuation and later resume the
  same invocation.

This is a product property of proto-go, not a temporary implementation
limitation.

## Decision

proto-go is a main-agent-orchestrated skill procedure.

A logical proto-go operation is initiated through the user-facing `/go`
invocation from a main-agent session.

The `/go` skill supplies the procedure governing the main agent's active
proto-go progression.

Conceptually:

```text
user
  ↓
main-agent session
  ↓
implementation request + /go
  ↓
/go skill
  ↓
main agent follows proto-go procedure
```

## First procedural step

The first procedural step performed by the main agent after `/go` invocation is
a terminating invocation of the proto-go script.

The ordering is:

```text
/go invoked
        ↓
main agent invokes proto-go script
        ↓
proto-go script executes
        ↓
proto-go script terminates
        ↓
script outputs return to main agent
        ↓
main agent continues proto-go procedure
```

Subsequent proto-go procedure steps use the state and outputs established by the
script while pursuing the already-established proto-go lifecycle.

This ADR does not define the script pathname, CLI, implementation language,
output schema, or internal mechanism.

## No script-to-main-agent suspension/resumption

An active proto-go script invocation cannot require this interaction:

```text
proto-go script begins
        ↓
mechanical work
        ↓
yield to main agent
        ↓
main agent performs authored continuation
        ↓
resume same script invocation
```

That interaction is outside the proto-go execution model.

A proto-go script call is terminating:

```text
main agent invokes
        ↓
script runs
        ↓
script terminates
        ↓
control returns to main agent
```

Main-agent authored work occurs after control has returned to the main agent,
not as a continuation suspended inside an active proto-go script invocation.

## Orchestration authority

The proto-go script is a mechanical capability invoked by the proto-go
procedure.

It is not the end-to-end workflow orchestrator.

The main agent, operating under the `/go` skill instructions, owns active
procedural orchestration.

This distinction does not transfer Product Intent authority to the main agent.

Product meaning remains governed by the normative proto-go specification and
accepted decisions.

The main agent executes the procedure; it does not invent missing semantics.

## Relationship to the lifecycle

The execution model established here composes with the existing lifecycle:

```text
user invokes /go
        ↓
main agent invokes proto-go script
        ↓
script terminates and returns outputs
        ↓
main agent continues managed authoring / progression
        ↓
governing validation obligations satisfied
        ↓
READY FOR HANDOFF
        ↓
downstream progression
        ↓
governing publication outcome
        ↓
proto-go success
```

Mechanical downstream blockage may still return authored work to the main agent
under the same logical proto-go objective as already established by ADR-001 and
the current specification.

Any further mechanical call made during that progression must not be interpreted
as having implicit ability to suspend into a main-agent continuation unless a
later explicit proto-go semantic decision establishes such a capability.

## Supersession of PROTO-GO-INV-012

`PROTO-GO-INV-012 — Product semantics are execution-engine independent` is
superseded by this ADR.

Its assertion is no longer normative.

Its identifier is not reused for a different invariant.

The current execution-model requirements are instead recorded as new invariants:

```text
PROTO-GO-INV-023
PROTO-GO-INV-024
PROTO-GO-INV-025
```

This preserves invariant identity history rather than assigning a new meaning to
an existing identifier.

## Consequences

The following consequences are accepted:

1. proto-go is not execution-engine independent.

2. Ordinary proto-go invocation occurs through `/go` in a main-agent session.

3. The `/go` skill governs the main agent's active proto-go procedure.

4. The first procedural step is invocation of the proto-go script.

5. The proto-go script invocation terminates before subsequent main-agent
   procedure steps execute.

6. The proto-go script cannot depend on yielding to a main-agent continuation
   and later resuming the same invocation.

7. The script is a mechanical capability, not the end-to-end proto-go
   orchestrator.

8. Main-agent authored work occurs while the main agent holds procedural control
   under the `/go` skill.

9. Existing lifecycle, readiness, validation, publication, multi-repository, and
   all-or-none publication semantics remain unchanged except where they had
   incorrectly assumed execution-engine independence.

## Non-decisions

This ADR does not decide:

* the proto-go script pathname;
* the proto-go script filename;
* the proto-go script programming language;
* the proto-go script CLI;
* the script output format;
* script persistence mechanisms;
* the exact state produced by the script;
* authoring-isolation architecture;
* whether Git worktrees are used;
* `/go` skill installation or discovery mechanics;
* harness implementation;
* main-agent implementation, vendor, or model;
* interruption recovery;
* whether a logical proto-go operation may be resumed from another main-agent
  session;
* lifecycle-state persistence;
* readiness representation;
* downstream API shape;
* publication-policy authority;
* route-specific publication semantics;
* GCP changes;
* Ruu changes.

Those questions require separate derivation or explicit decisions.
