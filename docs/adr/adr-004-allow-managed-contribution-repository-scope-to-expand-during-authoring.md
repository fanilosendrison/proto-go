# ADR-004: Allow ManagedContribution repository scope to expand during authoring

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-002 establishes that one logical `proto-go` operation owns exactly one
`ManagedContribution` and that one `ManagedContribution` may span one or more
repositories.

The remaining product question was whether every participating repository must
be known before managed authoring begins or whether the logical contribution may
discover and include additional repositories during implementation.

The product owner has resolved this question.

## Decision

The repository scope of a `ManagedContribution` may expand dynamically during
authoring.

The complete repository set does not need to be known when the logical `proto-go`
operation begins.

When implementation discovers that another repository must participate, that
repository may become part of the existing `ManagedContribution`.

Adding a repository does not create:

```text
a new implementation request
a new logical proto-go operation
a new ManagedContribution
```

However, before the first managed authoring mutation belonging to the
`ManagedContribution` occurs in a newly included repository, that repository
must already be under the contribution's managed authoring authority.

The forbidden ordering is:

```text
mutate repository
        ↓
later decide that the mutation belonged to the ManagedContribution
```

The required ordering is:

```text
ManagedContribution already exists
        ↓
repository becomes part of its managed authoring scope
        ↓
managed authoring conditions are established
        ↓
first managed authoring mutation in that repository
```

## Readiness consequence

A `READY FOR HANDOFF` occurrence applies to a determinate authored state of the
`ManagedContribution`.

That readiness occurrence therefore binds the repository participation and
authored state to which the readiness decision applies.

Conceptually:

```text
ManagedContribution A

authoring:
  repository X
  repository Y

READY #1
  binds authored state S1
  binds repository participation {X, Y}
```

If authored work later changes the contribution, including by adding another
repository, the resulting authored state is not automatically authorized by
`READY #1`.

For example:

```text
READY #1
  state S1
  repositories {X, Y}

        ↓

authoring resumes
repository Z is admitted
state changes to S2

        ↓

READY #1 does not authorize S2

        ↓

applicable validation/readiness obligations

        ↓

READY #2
  state S2
  repositories {X, Y, Z}
```

The logical `ManagedContribution` remains the same.

## Stable handoff consequence

Downstream progression for one `READY FOR HANDOFF` occurrence must operate on
the authored state bound by that readiness occurrence.

The Development System must not continue changing that authored state underneath
the downstream progression while treating the same readiness occurrence as
authority for the resulting moving target.

This restriction concerns authored mutation.

It does not prohibit downstream mechanical version-control realization of the
bound state.

## Consequences

The following consequences are accepted:

1. Repository discovery may occur during authoring.

2. Repository membership is not required to be sealed when `proto-go` begins.

3. A newly needed repository may join the existing `ManagedContribution`.

4. It must enter managed authoring authority before its first managed authoring
   mutation belonging to the contribution.

5. A readiness occurrence applies to a determinate authored state.

6. Repository participation relevant to that state is part of what the
   readiness occurrence covers.

7. Later authored mutation cannot silently inherit the authority of the earlier
   readiness occurrence.

8. A later valid authored state may require a new readiness occurrence.

9. Downstream progression for one readiness occurrence operates against its
   bound authored state rather than a moving authored target.

## Non-decisions

This ADR does not decide:

* how repository admission is represented;
* how repository participation is persisted;
* whether Git worktrees provide authoring isolation;
* whether one worktree exists per participating repository;
* whether repositories may later be removed from a contribution;
* whether repository-local child objects exist;
* repository-local readiness semantics;
* the exact representation of a readiness occurrence;
* readiness identifiers;
* validation evidence representation;
* downstream handoff representation;
* publication coordination;
* publication atomicity implementation;
* process topology.

Those questions require separate derivation or explicit decisions.
