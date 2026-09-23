# ADR-002: Allow one ManagedContribution to span multiple repositories

- Status: Accepted
- Date: 2026-09-23

## Context

The Product Intent defines one logical `proto-go` implementation request as producing
one managed implementation contribution.

The initial specification did not decide whether that contribution was
necessarily repository-local or whether one logical implementation occurrence
could produce coordinated managed effects across several repositories.

Both interpretations were compatible with the bootstrap Product Intent.

The product owner has resolved this question.

## Decision

One logical `proto-go` operation owns exactly one `ManagedContribution`.

A `ManagedContribution` represents one logical implementation occurrence.

It is not identified by a repository and is not equivalent to a
repository-local change.

One `ManagedContribution` may produce managed authoring effects in one or more
repositories while remaining one logical contribution.

Therefore:

```text
LogicalProtoGoOperation
        ↓
ManagedContribution
        ├── managed effects in repository A
        ├── managed effects in repository B
        └── managed effects in repository C
```

is a valid product shape.

The repository boundary does not determine contribution identity.

Normatively:

```text
ManagedContribution != Repository
```

and:

```text
one ManagedContribution
→ one or more repositories
```

is permitted.

## Contribution identity remains logically distinct

Because a `ManagedContribution` is the logical product of one `proto-go`
implementation occurrence, its identity must remain distinct from incidental or
mechanism-level identities including:

```text
repository
branch
worktree
session
agent
process
mutable authoring surface
```

This distinction does not prohibit an implementation from binding a
`ManagedContribution` to mechanism-specific objects.

It prohibits those objects from becoming the product definition of
`ManagedContribution`.

## Consequences

The following consequences are accepted:

1. One logical `proto-go` operation owns exactly one `ManagedContribution`.

2. A `ManagedContribution` may affect exactly one repository.

3. A `ManagedContribution` may also affect more than one repository.

4. Repository boundaries do not split one logical implementation occurrence
   into multiple `ManagedContribution` identities merely because several
   repositories are touched.

5. `ManagedContribution` identity must not be defined as repository identity.

6. The implementation must be capable of preserving the logical identity of a
   multi-repository contribution across its managed lifecycle.

7. The Product Intent does not require the user to manually decompose one
   logical implementation request into repository-local `proto-go` operations merely
   because the implementation spans repository boundaries.

## Non-decisions

This ADR does not decide:

* how repository-local effects are represented;
* whether a subordinate repository-local product object exists;
* what such an object would be named;
* how many mutable authoring surfaces a multi-repository contribution uses;
* whether Git worktrees are used;
* how many worktrees are used;
* whether all repositories are provisioned eagerly or lazily;
* whether repository-local readiness is tracked independently;
* whether all repository-local effects become ready simultaneously;
* whether publication across repositories is atomic;
* how multi-repository publication is coordinated;
* how downstream version-control systems receive a multi-repository
  contribution;
* how `git-commits-push` represents such work;
* how Ruu represents such work;
* whether `proto-go` and a downstream system share any identifier format;
* how `ManagedContribution` identity is persisted.

Those questions require separate derivation or explicit product decisions.
