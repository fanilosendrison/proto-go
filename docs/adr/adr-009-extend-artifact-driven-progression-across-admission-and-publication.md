# ADR-009: Extend artifact-driven progression across admission and publication

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-008 established Invocation Preflight and Admission before mechanical
execution, required an admission-complete machine-readable Launch Contract as
the initial authority root of an admitted logical proto-go operation, and
established artifact-driven main-agent continuation between fresh terminating
proto-go script invocations.

One element of ADR-008's ordering was too restrictive.

ADR-008 required the Launch Contract to be admission-complete before any
proto-go script invocation could occur.

That prevents the script from participating in the pre-Admission progression
that may be necessary to establish the Launch Contract itself.

The product model is instead a generic, state-driven, artifact-driven loop that
begins before Admission and continues through the governing publication outcome.

## Decision

A `/go` invocation is governed by the `/go` skill and produces a fresh sequence
of terminating proto-go script invocations separated by authorized main-agent
continuations.

Conceptually:

```text
user request + /go
        ↓
/go skill governs main agent
        ↓
main agent derives the best currently available
machine-readable input
        ↓
fresh proto-go script invocation
        ↓
script receives / resolves sufficient authoritative
Progression Context
        ↓
script observes current progression state
        ↓
script performs currently legal mechanical progression
        ↓
script emits Continuation Artifact
        ↓
script terminates
        ↓
main agent receives / resolves sufficient authoritative
Progression Context + Continuation Artifact
        ↓
/go Continuation Policy determines authorized continuation
        ↓
main agent performs continuation
        ↓
possibly:
- context resolution
- user interaction
- authority acquisition
- authored work
- construction of new machine-readable input
        ↓
fresh script invocation
        ↺
```

The loop begins before Admission.

It continues after Admission.

It may traverse multiple readiness occurrences.

Its normal successful terminal outcome is the governing publication outcome:

```text
PUBLISHED
```

`READY FOR HANDOFF` is not normal loop termination.

## Pre-Admission progression

Invocation Preflight may itself use terminating proto-go script invocations and
artifact-driven main-agent continuations before an admission-complete Launch
Contract exists.

Conceptually:

```text
/go
        ↓
main agent derives best currently available machine-readable input
        ↓
fresh script invocation
        ↓
script evaluates / progresses pre-Admission state
        ↓
Continuation Artifact
        ↓
script terminates
        ↓
main-agent continuation
        ↺
```

If required Admission information is missing:

```text
Admission forbidden
```

but:

```text
pre-Admission script invocation allowed
```

The script may mechanically establish that information or authority is missing
and produce the corresponding Continuation Artifact.

Missing Admission information or authority must prevent Admission, but must not
by itself prohibit a pre-Admission proto-go script invocation.

This progression is governed by `PROTO-GO-INV-038`.

## Launch Contract

The Launch Contract remains the initial authority root of an admitted logical
proto-go operation.

Correct model:

```text
user request
+
authoritative context
+
pre-Admission script / artifact / continuation progression
        ↓
sufficiently resolved intent + authority + launch premises
        ↓
admission-complete Launch Contract
        ↓
Admission
```

The Launch Contract may be established through multiple terminating script
invocations and main-agent continuations.

Neither the script nor the main agent may invent missing semantic authority.

This progression is governed by `PROTO-GO-INV-039`.

The Launch Contract's schema, storage, serialization, and versioning remain
open.

## Admission boundary

Before Admission:

```text
pre-Admission progression may exist

logical proto-go operation not yet admitted

ManagedContribution not yet admitted
```

At Admission:

```text
admission-complete Launch Contract
        ↓
logical proto-go operation
        ↓
exactly one ManagedContribution
```

Existing contribution identity invariants remain unchanged.

## First post-Admission mechanical transition

`PROTO-GO-INV-033` remains normative.

After Admission, the first mechanical transition of the admitted operation is a
fresh terminating script invocation.

Therefore:

```text
pre-Admission invocation
→ artifact
→ terminates
→ Admission
→ fresh post-Admission invocation
```

A pre-Admission process invocation must not be reinterpreted as continuing
across Admission.

## `/go` re-entry

A Continuation Artifact may lead `/go` to require user input.

Example:

```text
script invocation #N
        ↓
Continuation Artifact establishes missing user authority
        ↓
script terminates
        ↓
/go Continuation Policy
        ↓
main agent asks user
```

The user may later answer with a new message containing `/go`.

Example:

```text
user:
"yes, use repository X /go"
```

When that `/go` is the response to an outstanding authorized continuation:

```text
new /go invocation
→ continues relevant existing progression
```

It must not create a distinct logical objective merely because `/go` appeared
again.

Before Admission:

```text
same pre-Admission progression continues
```

After Admission:

```text
same logical proto-go operation
same ManagedContribution
```

Every resulting script execution remains a fresh invocation.

This re-entry semantics is governed by `PROTO-GO-INV-040`.

This ADR does not decide how progression correlation or rediscovery works.

## Generic state-driven progression

proto-go must not be modeled as a universal fixed stage pipeline such as:

```text
setup
→ implementation
→ tests
→ READY
→ publication
```

There is no universal ordered stage list.

At each script invocation:

```text
current authoritative progression state
+
current governing authority / contracts
+
new authoritative input
        ↓
determine mechanically legal progression
```

The script must not depend on a numbered step unless a later architecture
chooses such a representation.

The product requirement is state/authority based, not numbered-step based.

## Progression through READY

`READY FOR HANDOFF` must not terminate normal artifact-driven progression.

Example:

```text
READY #1
        ↓
publication progression
        ↓
mechanical block
        ↓
Continuation Artifact
        ↓
retire publication authority #1
        ↓
fence #1
        ↓
script terminated
        ↓
main-agent authored correction
        ↓
fresh script progression
        ↓
READY #2
        ↓
publication progression
```

`PROTO-GO-INV-026` through `PROTO-GO-INV-028` remain fully normative.

No authored mutation after a publication-authorizing readiness occurrence may
bypass retirement and fencing.

## Successful terminal condition

Normal successful termination remains the governing publication outcome:

```text
governing publication outcome
        ↓
PUBLISHED
```

This preserves ADR-001 and `PROTO-GO-INV-008`.

This ADR does not redefine publication semantics.

Progression through READY to the governing publication outcome is governed by
`PROTO-GO-INV-041`.

## Progression Context

The canonical concept required by this ADR is `Progression Context`:

```text
The machine-readable authoritative context sufficient for an actor receiving
proto-go control to continue the relevant progression correctly without
depending on implicit conversational memory.

Progression Context represents the current authoritative progression state and
the relevant provenance, authority, contracts, established facts, and prior
continuation information necessary for correct subsequent progression.

It does not require retention or replay of irrelevant complete history.

Its concrete representation, storage, transport, persistence, reconstruction,
snapshotting, event history, and ownership are not defined.
```

Progression Context is not defined as necessarily:

```text
one file
one JSON object
one database record
one event log
one snapshot
one artifact chain
one Turnlock object
```

Those are architecture questions.

At every script invocation, the proto-go script must have sufficient explicit
machine-readable authoritative context to determine:

```text
which progression is concerned
what state has already been established
what relevant authority / contracts govern it
what relevant prior continuation has occurred
what facts remain valid
what mechanical transitions are currently permitted
```

The script must not depend on implicit conversational memory of the main agent.

When a script invocation terminates, the main agent must be able to understand
the resulting continuation without depending on implicit memory of prior chat
turns.

The receiving main agent must have enough explicit context to establish, where
relevant:

```text
which progression this is
what authoritative state existed before the invocation
what mechanical progression just occurred
what facts were established
what authority / contracts are relevant
what continuation condition was reached
what prior decisions constrain the next action
```

This does not require literal replay of every historical event.

Only history and provenance necessary for correct continuation are required.

## Control-transfer symmetry

Control transfer must be symmetric in its context requirement.

For:

```text
main agent → script
```

the requirement is:

```text
sufficient authoritative Progression Context
+
current machine-readable invocation input
```

For:

```text
script → main agent
```

the requirement is:

```text
sufficient authoritative Progression Context
+
Continuation Artifact
```

The following is explicitly rejected:

```text
correctness depends on "the agent remembering what happened"
```

It is also insufficient to depend on a prior script process surviving.

Complete literal replay of all historical events is not required:

```text
sufficient relevant prior context
!= complete literal replay of all historical events
```

This control-transfer requirement is governed by `PROTO-GO-INV-042`.

## Supersession of PROTO-GO-INV-030

`PROTO-GO-INV-030 — Admission requires a machine-readable Launch Contract` is
superseded by this ADR.

The requirement that Admission requires an admission-complete Launch Contract
remains part of current Product Intent.

This ADR supersedes the previous allocation and ordering by allowing Launch
Contract completeness to be established through pre-Admission artifact-driven
progression involving terminating proto-go script invocations and authorized
main-agent continuations.

The corrected requirement is represented by `PROTO-GO-INV-039`.

The identifier of `PROTO-GO-INV-030` is retained only for invariant identity
history and must not be reused.

## Supersession of PROTO-GO-INV-032

`PROTO-GO-INV-032 — Missing admission authority fails closed` is superseded by
this ADR.

The fail-closed Admission boundary is preserved.

What is superseded is the prohibition on pre-Admission script execution.

Missing Admission information or authority still prevents Admission.

It does not by itself prevent a terminating proto-go script invocation during
Invocation Preflight.

The corrected semantics are represented by `PROTO-GO-INV-038` and
`PROTO-GO-INV-039`.

The identifier of `PROTO-GO-INV-032` is retained only for invariant identity
history and must not be reused.

## Relationship to ADR-008

ADR-008 remains accepted historical decision record.

Its Admission boundary, Launch Contract authority-root semantics, terminating
script invocations, artifact-driven continuation, and `/go`-owned continuation
policy remain valid.

ADR-008's requirement that an admission-complete Launch Contract must exist
before any script invocation is corrected by this ADR.

Invocation Preflight may use terminating script invocations and artifact-driven
continuations before an admission-complete Launch Contract exists.

## Relationship to ADR-007

ADR-007 remains fully normative.

Artifact-driven authored correction after readiness does not bypass retirement
and fencing of prior publication authority.

## Consequences

The following consequences are accepted:

1. Artifact-driven progression begins before Admission.

2. Pre-Admission script invocation may run with incomplete Admission
   information.

3. Missing Admission authority still prevents Admission.

4. Launch Contract completeness may be established through artifact-driven
   progression.

5. The Launch Contract remains the initial authority root.

6. `/go` may re-enter an outstanding authorized continuation.

7. Re-entry does not by itself create a new logical objective.

8. Each script invocation remains fresh and terminating.

9. The script progresses from authoritative state rather than a fixed numbered
   step.

10. `READY FOR HANDOFF` remains intermediate.

11. Progression continues through publication.

12. `PUBLISHED` remains the normal successful terminal outcome.

13. Every main-agent/script control transfer requires sufficient
    machine-readable authoritative Progression Context.

14. Script correctness does not depend on main-agent conversational memory.

15. Main-agent continuation correctness does not depend on remembered prior
    chat.

16. Complete irrelevant historical replay is not required.

## Non-decisions

This ADR does not decide:

* Progression Context representation;
* snapshot vs event-log model;
* persistence mechanism;
* history-retention mechanism;
* context reconstruction algorithm;
* correlation or re-entry mechanism;
* Launch Contract schema;
* Launch Contract storage;
* Launch Contract serialization;
* Launch Contract versioning;
* invocation-input schema;
* Continuation Artifact schema;
* Continuation Policy implementation;
* fixed stage pipeline;
* workflow engine;
* Turnlock integration;
* database selection;
* IPC or event mechanism;
* publication-authority rules;
* publication route selection;
* GCP integration;
* Ruu integration.

Those questions require separate derivation or architectural decisions.
