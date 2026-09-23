# ADR-014: Make multi-repository publication an aggregate completion condition

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-005 established a stronger semantic than the product owner actually
intended.

ADR-005 required all-or-none governing publication visibility across all
repository effects bound by one readiness occurrence.

The actual Product Intent is different:

```text
proto-go may publish the repository-local effects of one readiness occurrence
independently and at different times.

proto-go MUST NOT consider the ManagedContribution PUBLISHED
until every repository-local publication requirement bound by the
currently publication-authorizing readiness occurrence has been satisfied.
```

The product requires aggregate publication completion.

It does not require atomic cross-repository publication visibility.

## Decision

A `READY FOR HANDOFF` occurrence binds the complete determinate authored state
and repository participation already required by existing Product Intent.

For publication purposes, that readiness occurrence establishes the complete
set of repository-local publication requirements applicable to that bound state.

Canonicalize the concept:

```text
Repository Publication Obligation
```

Define it as:

> A governing repository-local publication requirement, associated with one
> readiness occurrence, for the determinate authored state bound for one
> participating repository under the applicable version-control policy.

A readiness occurrence may therefore conceptually establish:

```text
READY #N

Repository Publication Obligations:

OA = repository A / bound state SA / governing publication requirement PA
OB = repository B / bound state SB / governing publication requirement PB
OC = repository C / bound state SC / governing publication requirement PC
```

Each obligation may become satisfied independently.

The following intermediate state is explicitly conformant:

```text
OA = SATISFIED
OB = UNSATISFIED
OC = UNSATISFIED

ManagedContribution = NOT PUBLISHED
```

Likewise:

```text
OA = SATISFIED
OB = SATISFIED
OC = UNSATISFIED

ManagedContribution = NOT PUBLISHED
```

There is no Product Intent requirement that the external publication outcomes
become visible atomically across repositories.

No distributed transaction, common activation point, rollback protocol,
staging-ref protocol, publication manifest, cross-repository transaction, or
similar mechanism is required merely by multi-repository participation.

The governing `PUBLISHED` fact of the `ManagedContribution` is established only
when:

```text
1. there is an effective publication-authorizing readiness occurrence R

AND

2. every Repository Publication Obligation established for R
   is authoritatively satisfied
```

Conceptually:

```text
effective READY R
+
all publication obligations of R satisfied
↓
ManagedContribution PUBLISHED
```

A proper subset of repository-local publication obligations being satisfied
MUST NOT establish `PUBLISHED`.

A repository-local publication fact is not itself a partial proto-go success.

There remains exactly one logical proto-go operation and one
`ManagedContribution`.

## New readiness and previously published reality

A later readiness occurrence defines its own Repository Publication
Obligations.

It does not inherit obligation satisfaction merely because an earlier readiness
occurrence had an obligation with similar identity.

Instead, each obligation of the later readiness occurrence is evaluated against
current authoritative publication reality.

Example:

```text
READY #1:
  A@SA
  B@SB

A@SA reaches its governing repository-local publication outcome.

READY #1 is later retired.

B is corrected:

  SB → SB2

READY #2:
  A@SA
  B@SB2
```

READY #2 defines its own obligations:

```text
OA2 = A@SA under READY #2 governing publication requirement
OB2 = B@SB2 under READY #2 governing publication requirement
```

If current authoritative publication reality already satisfies OA2 exactly, then:

```text
OA2 = already satisfied
```

No redundant republication is required merely because READY #2 is a different
readiness occurrence.

This is not inheritance from READY #1.

It is independent evaluation of READY #2's own obligation against authoritative
current reality.

If the bound authored state or governing publication requirement differs, prior
publication does not automatically satisfy the new obligation.

For example:

```text
READY #1:
  A@SA

READY #2:
  A@SA2
```

then publication of `SA` does not satisfy the obligation to publish `SA2`.

Likewise, satisfaction under one publication destination/policy does not
automatically satisfy a materially different governing publication requirement.

## Relationship to ADR-005

ADR-005 remains immutable historical decision record.

ADR-014 supersedes ADR-005's all-or-none / indivisible cross-repository
publication-visibility semantics.

Specifically superseded are the assertions that:

```text
a proper repository subset may not independently reach its governing publication outcome

partial governing repository publication is inherently non-conformant

a downstream route must provide all-or-none cross-repository governing publication visibility

proto-go must refuse a route merely because repository-local publication effects
cannot cross their boundaries atomically
```

Preserve the useful part of the earlier intent:

```text
one readiness occurrence defines one aggregate publication-completion condition
for one ManagedContribution
```

but do not describe that condition as atomic visibility or an indivisible
external publication boundary.

ADR-014 supersedes:

```text
PROTO-GO-INV-020
PROTO-GO-INV-021
PROTO-GO-INV-022
```

## Consequences

The following consequences are accepted:

1. Repository-local publication outcomes may occur independently and at
   different times.

2. A `ManagedContribution` remains not `PUBLISHED` while any publication
   obligation of its effective readiness occurrence remains unsatisfied.

3. Multi-repository publication does not intrinsically require a
   cross-repository transaction.

4. Repository-local downstream publication primitives remain compatible with
   the Product Intent when proto-go correctly aggregates their authoritative
   outcomes.

5. Publication progress must be tracked at repository-obligation granularity.

6. A later readiness occurrence independently evaluates its own obligations
   against current authoritative publication reality.

7. Already-satisfied identical publication reality may satisfy a later
   readiness obligation without repeating the mechanical publication.

8. Repository-local publication facts remain distinct from the global
   `PUBLISHED` fact of the `ManagedContribution`.

## Non-decisions

This ADR does not decide:

* downstream implementation;
* GCP integration mechanics;
* Ruu integration mechanics;
* publication sequencing;
* publication parallelism;
* retry scheduling;
* Repository Publication Obligation serialization;
* Repository Publication Obligation identifier format;
* storage mechanism;
* publication receipt format;
* route-specific definition of repository-local publication;
* branch/ref policy;
* pull-request publication semantics;
* merge-queue semantics;
* base-commit policy.
