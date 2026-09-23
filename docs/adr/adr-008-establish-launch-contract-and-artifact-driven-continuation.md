# ADR-008: Establish Launch Contract and artifact-driven continuation

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-006 established that proto-go is orchestrated by the main agent under the
`/go` skill and that an active proto-go script invocation cannot suspend into
main-agent authored work and later resume the same invocation.

ADR-006 also recorded the proto-go script as the first procedural step after
`/go` invocation.

That ordering is no longer correct.

Before mechanical execution begins, the main agent must first establish that the
user request and available governing context contain sufficient intent,
authority, and launch premises to admit a logical proto-go operation.

The user may need to clarify missing information before admission.

The result of that resolution must be available in machine-readable form so that
mechanical execution does not depend on implicit conversational understanding.

A second execution requirement has also been clarified.

A logical proto-go operation may need to alternate between mechanical execution
and main-agent action.

The required interaction is not suspension and resumption of one script
process.

Instead, each script invocation terminates, emits machine-readable result
artifacts, and returns control to the main agent. The `/go` procedure interprets
those artifacts under predefined continuation policy. The main agent performs
the authorized continuation and may later invoke the script again as a new
invocation.

## Decision

A `/go` invocation enters an Invocation Preflight before a logical proto-go
operation is admitted.

Conceptually:

```text
user implementation request + /go
        ↓
Invocation Preflight
        ↓
main agent resolves intent + authority + launch premises
        ↓
Launch Contract admission-complete?
        │
        ├── no
        │     ↓
        │  resolve from authoritative context where possible
        │     ↓
        │  obtain user clarification when user authority is required
        │     ↺
        │
        └── yes
              ↓
Admission
              ↓
logical proto-go operation begins
```

An incomplete `/go` invocation does not by itself admit a logical proto-go
operation or ManagedContribution.

## Launch Contract

Admission requires a machine-readable `Launch Contract`.

The Launch Contract represents the resolved implementation intent, governing
authority basis, and launch premises with which the logical proto-go operation
is admitted.

Conceptually:

```text
conversation + authorized context
        ↓
main-agent semantic resolution
        ↓
machine-readable Launch Contract
        ↓
Admission
```

The Launch Contract is the initial authority root of the admitted operation.

Normative contracts, obligations, or semantic decisions used later by proto-go
must derive from:

* authority represented by the admitted Launch Contract;
* authoritative facts resolved under that authority; or
* explicit additional authority obtained through an authorized continuation.

The main agent, proto-go script, and downstream mechanical systems do not gain
authority to invent missing semantics merely because progression requires them.

This ADR does not define the Launch Contract schema, serialization, persistence,
storage, transport, or versioning.

## Admission completeness fails closed

If information or authority required for admission cannot be established with
sufficient authority, proto-go does not admit the logical operation and does not
invoke the proto-go script for that operation.

The main agent first resolves what can be established from sufficiently
authoritative available context.

If a remaining admission requirement requires user authority, the main agent
asks the user for the required clarification or decision.

Invocation Preflight may therefore contain multiple main-agent/user turns before
Admission.

This rule concerns information required for Admission.

It does not require all future implementation facts, repositories, validation
obligations, or publication facts to be known before Admission.

## First mechanical transition

After Admission, the first mechanical transition of the logical proto-go
operation is a fresh terminating invocation of the proto-go script.

Conceptually:

```text
Admission
        ↓
derive machine-readable invocation input
        ↓
invoke proto-go script
        ↓
mechanical execution
        ↓
emit machine-readable Continuation Artifact information
        ↓
script terminates
        ↓
control returns to main agent
```

The invocation input is derived from the admitted Launch Contract and applicable
authoritative operation state.

The Launch Contract is not defined as equivalent to the concrete invocation
input.

This ADR does not define the invocation-input schema, serialization, transport,
CLI, or storage.

## Artifact-driven continuation

Each completed proto-go script invocation emits machine-readable result artifact
information sufficient for the `/go` procedure to classify the mechanical
result and determine the applicable continuation rule.

This result information is canonicalized as a `Continuation Artifact`.

A Continuation Artifact may carry mechanical facts or continuation conditions.

It does not itself hold procedural authority.

The authority relationship is:

```text
proto-go script
→ emits machine-readable facts / continuation condition

/go skill
→ owns continuation policy

main agent
→ executes the authorized continuation
```

The script therefore does not gain authority to issue arbitrary free-form
commands to the main agent.

The `/go` continuation policy defines how recognized artifact facts or
conditions map to permitted or required main-agent behavior.

That policy may include predefined heuristics, but those heuristics remain
subordinate to proto-go Product Intent and applicable governing authority.

## Terminating-script continuation loop

A logical proto-go operation may contain multiple script invocations.

Each invocation is terminating.

Main-agent continuation occurs only after the preceding invocation has
terminated.

Conceptually:

```text
                 ┌──────────────────────────────┐
                 │                              │
                 ▼                              │
        fresh script invocation                 │
                 ↓                              │
        mechanical execution                    │
                 ↓                              │
       Continuation Artifact                    │
                 ↓                              │
          script terminates                     │
                 ↓                              │
      /go continuation policy                   │
                 ↓                              │
        main-agent continuation                 │
                 ↓                              │
     derive next invocation input ──────────────┘
```

A later invocation is a new invocation.

It is not resumption of the prior terminated invocation.

Therefore:

```text
script invocation
= terminating mechanical transition

logical proto-go operation
= potentially iterative workflow
```

## Main-agent continuation

An authorized main-agent continuation may include, when permitted by `/go`
continuation policy and governing authority:

* authored work;
* context resolution;
* obtaining additional authoritative facts;
* interaction with the user;
* acquisition of additional user authority;
* construction of machine-readable state or input for later progression;
* direct continuation of the proto-go procedure.

These are categories of possible continuation, not a fixed continuation enum.

This ADR does not define concrete Continuation Artifact classes.

## Post-admission user interaction

User interaction is permitted after Admission when reached through an authorized
artifact-driven continuation.

For example:

```text
script invocation #N
        ↓
artifact establishes that additional user authority is required
        ↓
script invocation #N terminates
        ↓
/go continuation policy selects user-resolution continuation
        ↓
main agent asks user
        ↓
user supplies required decision / authority
        ↓
main agent derives authoritative new state/input
        ↓
fresh script invocation #N+1
```

No script invocation is active while the main agent is interacting with the
user.

This is therefore compatible with the no-suspension rule established by
ADR-006.

This ADR does not decide whether later authority is represented by mutating the
Launch Contract, versioning it, attaching amendments, or another mechanism.

## Relationship to ADR-006

ADR-006 remains accepted historical decision record.

Its terminating-script and main-agent-orchestration semantics remain valid.

However, the assertion that script invocation is the first procedural step is
superseded.

Invocation Preflight and Admission now precede the first mechanical script
transition.

`PROTO-GO-INV-024 — First procedural step is a terminating proto-go-script
invocation` is therefore superseded by this ADR.

Its identifier remains historical and is not reused.

`PROTO-GO-INV-025 — Active script execution cannot contain a suspended
main-agent continuation` remains normative.

The new model strengthens rather than removes that boundary:

```text
script invocation #N
→ terminates
→ main-agent continuation
→ optionally fresh script invocation #N+1
```

## Relationship to existing lifecycle semantics

This ADR changes neither the identity nor publication lifecycle established by
the existing invariants.

After Admission:

```text
one logical proto-go operation
→ exactly one ManagedContribution
```

Managed authoring, validation, readiness, publication-unit, fencing, and
publication semantics continue to apply unchanged.

Invocation Preflight occurs before that admitted operation.

Artifact-driven continuations operate within the same logical proto-go operation
unless a separate accepted semantic rule says otherwise.

## Consequences

The following consequences are accepted:

1. `/go` invocation and logical-operation Admission are distinct boundaries.

2. Invocation Preflight occurs before Admission.

3. Invocation Preflight may loop with the user.

4. Admission requires a machine-readable Launch Contract.

5. The Launch Contract is the initial authority root of the operation.

6. Missing information or authority required for Admission fails closed.

7. The proto-go script is not the first procedural step.

8. The first mechanical transition after Admission is a fresh terminating
   proto-go script invocation.

9. Each completed script invocation emits machine-readable Continuation Artifact
   information.

10. Continuation Artifacts do not themselves hold procedural authority.

11. `/go` owns continuation policy.

12. The main agent executes authorized continuation under `/go` policy and
    governing authority.

13. Main-agent continuation occurs only after script termination.

14. A continuation may include user interaction after Admission.

15. The script may be invoked again after continuation.

16. Every later invocation is fresh rather than resumption of an earlier
    invocation.

17. One logical proto-go operation may therefore contain multiple terminating
    script invocations.

## Non-decisions

This ADR does not decide:

* the Launch Contract schema;
* Launch Contract serialization;
* Launch Contract storage;
* Launch Contract persistence;
* Launch Contract transport;
* Launch Contract versioning;
* whether later authority mutates the Launch Contract;
* whether later authority is represented as amendments;
* the script pathname;
* script implementation language;
* script CLI;
* invocation-input schema;
* invocation-input transport;
* Continuation Artifact schema;
* Continuation Artifact serialization;
* concrete Continuation Artifact classes;
* continuation-policy representation;
* continuation-policy implementation;
* exact heuristic algorithms;
* workflow persistence;
* workflow recovery;
* process supervision;
* Turnlock integration;
* database selection;
* IPC mechanism;
* queue or event mechanism;
* exact user-interaction protocol;
* validation-contract representation;
* publication-contract representation;
* publication-authority rules;
* route-selection policy;
* downstream implementation.

Those questions require separate derivation or architectural decisions.
