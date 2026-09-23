# ADR-010: Require temporary detached Git worktrees for managed authoring

- Status: Accepted
- Date: 2026-09-23

## Context

`PROTO-GO-INV-005` already requires distinct concurrently authored
`ManagedContribution` instances not to share an ordinary mutable authoring
surface.

That invariant established the isolation property without selecting the
mechanism that provides it.

Managed authoring must also not disturb the invoking checkout or the user's
ordinary checkout, and a `ManagedContribution` may span several repositories.

The product owner has resolved the authoring-isolation mechanism question.

## Decision

Managed authored mutation performed by proto-go must not occur in:

* the invoking checkout;
* the current ordinary checkout; or
* another `ManagedContribution`'s managed authoring worktree.

For each repository in which an admitted `ManagedContribution` requires managed
authored mutation, proto-go must automatically establish a dedicated Git
worktree before the first managed authored mutation in that repository.

The managed authoring worktree belongs semantically to:

```text
ManagedContribution × participating repository
```

It does not belong to a conversational session, main-agent process, script
invocation, or transient controller.

The worktree must be created from a determinate Git commit.

The managed authoring worktree must use detached HEAD.

Managed authoring must remain detached from an ordinary checked-out branch
while that worktree is the contribution's managed authoring surface.

A repository added dynamically to an existing `ManagedContribution` receives
its own managed worktree before the first managed authored mutation in that
repository.

Different concurrently authored `ManagedContribution` instances must not share
the same managed authoring worktree.

A change of main-agent session must not by itself create a replacement
authoring worktree for the same `ManagedContribution × repository`.

## Lifetime

A managed worktree survives as required through:

```text
script termination
main-agent / session replacement
READY FOR HANDOFF
publication attempts
publication blockage
readiness retirement and fencing
authored correction
revalidation
subsequent readiness occurrences
```

Until abandonment semantics are separately defined, session loss, process loss,
script termination, elapsed time, READY, or another incidental event must not by
itself authorize destruction of a managed worktree.

## Closure

The normal lifecycle includes automatic retirement and removal of the temporary
managed worktree after the governing publication outcome has been established
and the worktree is no longer needed for authored progression.

Managed worktree removal is a proto-go-owned closure obligation.

Failure to remove a worktree must not erase or falsify an already-established
`PUBLISHED` fact.

Failure to remove a worktree must be reported into the artifact-driven
continuation model established by ADR-013 rather than being silently ignored or
converted into successful completion.

This ADR does not define abandonment semantics.

## Relationship to PROTO-GO-INV-005

This ADR strengthens the isolation semantics already expressed by
`PROTO-GO-INV-005`.

It does not supersede `PROTO-GO-INV-005`.

## Non-decisions

This ADR does not decide:

* worktree filesystem path;
* worktree naming;
* base-commit selection algorithm;
* branch/ref naming;
* registry representation;
* reconstruction mechanism if a worktree is unexpectedly absent;
* garbage collection for abandoned progressions;
* forced worktree removal policy;
* Git command implementation.

Those remain architectural or separately derived decisions.
