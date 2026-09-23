# ADR-015: Allow originated repository publication to complete after readiness retirement

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-014 creates the following case:

```text
READY #1:

OA = repository A publication obligation
OB = repository B publication obligation
OC = repository C publication obligation

OA = satisfied
OB = blocked
OC = repository-local publication already in flight
```

Suppose resolving B requires managed authored mutation.

Existing ADR-007 correctly requires the prior readiness occurrence to lose
publication authority before authored mutation resumes.

However, ADR-007 was written under ADR-005's now-superseded atomic-publication
model and can be read as requiring already-originated downstream publication
activity to be fenced or cancelled before authoring resumes.

The product owner has explicitly rejected that stronger requirement.

## Decision

Before managed authored mutation resumes after a publication-authorizing
readiness occurrence:

```text
the readiness occurrence MUST lose authority
to establish the ManagedContribution's global PUBLISHED fact
```

and:

```text
the retired readiness occurrence MUST NOT authorize initiation
of any new repository-local publication operation
```

This retirement remains mandatory before the first subsequent managed authored
mutation.

However:

```text
repository-local publication operations already originated
before retirement
MAY continue and complete after retirement
```

proto-go is NOT required to cancel, fence, rollback, or wait for those
already-originated repository-local publication operations before allowing
managed authored mutation to resume.

Therefore the following is conformant:

```text
READY #1

A@SA → published
B@SB → blocked
C@SC → publication already in flight

↓
retire READY #1 global publication-completion authority
↓
READY #1 may initiate no new repository-local publication work
↓
authoring resumes for B
↓
C@SC publication finishes
```

Afterward all of the following may simultaneously be true:

```text
READY #1 was historically valid

READY #1 is retired

A@SA is authoritatively published

C@SC is authoritatively published

B@SB was not published

READY #1 can never establish ManagedContribution PUBLISHED
```

## Repository-local publication facts survive retirement

Retirement of a readiness occurrence MUST NOT erase or falsify repository-local
publication outcomes already established.

Likewise, a repository-local publication operation that was already originated
before retirement and completes afterward establishes an authoritative
publication fact if the governing repository policy says that publication
occurred.

Conceptually:

```text
historical repository publication fact
!= readiness publication-completion authority
```

The publication fact may later be relevant when evaluating a new readiness
occurrence's publication obligations.

## Retired readiness never regains global completion authority

Even if all repository-local obligations originally associated with a retired
readiness occurrence later become satisfied due to already-originated
operations:

```text
retired READY #1
+
all its former repository-local publication effects eventually exist
```

MUST NOT establish:

```text
ManagedContribution PUBLISHED
```

A retired readiness occurrence is never reactivated.

If authoring resumed, a later applicable readiness occurrence is required by
existing Product Intent.

That later readiness occurrence independently evaluates its own Repository
Publication Obligations against then-current authoritative publication reality.

## Example

Required conceptual example:

```text
READY #1:
  A@SA
  B@SB
  C@SC

A@SA → published
B@SB → blocked
C@SC → publication in flight

B requires authored correction
↓
retire READY #1
↓
no new publication operation may be initiated under READY #1
↓
author B:
  SB → SB2

during authoring:
C@SC → published

↓
validate current complete contribution state
↓
READY #2:
  A@SA
  B@SB2
  C@SC
```

READY #2 then evaluates:

```text
A@SA obligation
→ already satisfied if authoritative publication reality satisfies
   READY #2's exact governing requirement

B@SB2 obligation
→ unsatisfied

C@SC obligation
→ already satisfied if authoritative publication reality satisfies
   READY #2's exact governing requirement
```

After B@SB2 reaches its required publication outcome:

```text
all READY #2 publication obligations satisfied
+
READY #2 retains effective publication-completion authority
↓
ManagedContribution PUBLISHED
```

## Relationship to ADR-007

ADR-007 remains immutable historical decision history.

ADR-015 preserves these ADR-007 semantics:

```text
authored resumption requires retirement of prior readiness authority

retirement occurs before first subsequent managed authored mutation

historical readiness remains true

retired readiness is never reactivated

a later authored state requires a new readiness occurrence

at most one readiness occurrence may hold effective authority
to establish the ManagedContribution's governing PUBLISHED outcome
```

ADR-015 supersedes only the stronger interpretation that retirement requires
already-originated repository-local publication effects to be prevented from
completing before authored mutation may resume.

Under ADR-015:

```text
fencing required by ADR-007
=
fencing of the retired readiness from establishing global ManagedContribution
PUBLISHED and from initiating new repository-local publication operations
```

It does NOT mean:

```text
all already-originated repository-local publication operations
must be cancelled or prevented from reaching their repository-local outcomes
```

`PROTO-GO-INV-026`, `PROTO-GO-INV-027`, and `PROTO-GO-INV-028` remain normative.

Their reference to the governing publication boundary is now to be understood
consistently with ADR-014 and ADR-015 as the ManagedContribution-level
`PUBLISHED` boundary, not as a prohibition on independent repository-local
publication outcomes.

Do not alter their identifiers or mark them superseded.

## Non-decisions

This ADR does not decide:

* whether an implementation opportunistically cancels an in-flight operation;
* whether downstream cancellation exists;
* whether rollback exists;
* how retirement is represented;
* how initiation authority is represented;
* lock/lease/generation/token mechanics;
* storage;
* process supervision;
* retry mechanism;
* publication receipt schema;
* reconciliation mechanism.
