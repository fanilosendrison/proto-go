# ADR-005: Make each readiness occurrence an indivisible publication unit

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-001 establishes the governing publication outcome as the normal successful
terminal outcome of `proto-go`.

ADR-002 establishes that one `ManagedContribution` may span several
repositories.

ADR-004 establishes that a `READY FOR HANDOFF` occurrence applies to a
determinate authored state and repository participation.

A remaining product question was whether repositories participating in one
readiness occurrence may independently cross their governing publication
boundaries, leaving a logically partial publication state while the same
`ManagedContribution` remains incomplete.

The product owner has resolved this question.

## Decision

The managed effects bound by one `READY FOR HANDOFF` occurrence form one
indivisible logical publication unit.

The governing publication semantics for that unit are all-or-none.

No proper subset of the managed effects bound by the readiness occurrence may
independently reach its governing publication outcome while the remainder has
not.

Conceptually, for:

```text
READY #N
  binds:
    repository A effect
    repository B effect
    repository C effect
```

the following is not an acceptable governing publication state:

```text
A = published
B = not published
C = not published
```

Nor:

```text
A = published
B = published
C = not published
```

The publication boundary belongs to the logical publication unit established by
the readiness occurrence.

## Preparation is distinct from publication

This decision does not require every downstream mechanical step to occur
atomically.

Downstream progression may prepare repository-local publication effects
progressively.

For example, a downstream mechanism may create commits, remote preparatory refs,
staging state, or other intermediate materialization independently where the
governing policy permits it.

Such preparation is conformant only when those intermediate effects do not
independently constitute the governing publication outcome for a proper subset
of the logical publication unit.

Therefore:

```text
remote effect
!= necessarily governing publication outcome
```

The distinction between preparation and governing publication is semantic, not
merely local-versus-remote.

## Route capability requirement

`proto-go` must not begin a publication transition that can irreversibly expose a
partial governing publication outcome when the selected downstream route cannot
preserve the required all-or-none publication semantics.

Conceptually:

```text
route can preserve all-or-none governing publication
        ↓
publication progression may begin
```

whereas:

```text
route cannot preserve all-or-none governing publication
        ↓
do not begin an irreversible partial governing publication
```

The correct behavior is not to silently degrade to best-effort repository-local
publication.

## Strength of the guarantee

The required guarantee applies to the governing publication boundary itself.

This ADR does not require all preparatory mechanical operations to constitute one
physical distributed transaction.

It does require that the product must not treat or intentionally expose a proper
subset of the readiness occurrence as having independently reached its governing
publication outcome.

Rollback after already exposing an independently valid partial governing
publication does not, by itself, satisfy the stronger all-or-none product
semantics established here.

## Consequences

The following consequences are accepted:

1. One readiness occurrence defines one logical publication unit.

2. A multi-repository readiness occurrence is not a collection of independently
   publishable `proto-go` completions.

3. A proper subset must not independently reach its governing publication
   outcome.

4. Progressive preparation remains permitted.

5. Remote preparation may remain permitted when it is not itself the governing
   publication outcome.

6. Route capability must be sufficient to preserve all-or-none governing
   publication semantics.

7. If the selected route cannot preserve that property, `proto-go` must not start an
   irreversible partial governing publication through that route.

8. `proto-go` must not silently degrade the publication contract to best-effort
   repository-local progression.

## Non-decisions

This ADR does not decide:

* how all-or-none publication is implemented;
* whether a distributed transaction is used;
* whether an indirection layer is used;
* whether staging refs are used;
* whether a manifest is used;
* whether a final activation primitive is used;
* whether locks are used;
* whether rollback exists;
* compensation semantics;
* route-specific publication outcomes;
* pull-request publication semantics;
* merge-queue semantics;
* changes required in `git-commits-push`;
* changes required in Ruu;
* downstream API shape;
* publication-coordinator architecture;
* failure-state representation.

Those questions require separate derivation or explicit decisions.
