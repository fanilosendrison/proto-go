# ADR-001: Make publication the successful terminal outcome of proto-go

- Status: Accepted
- Date: 2026-09-23

## Context

The bootstrap Product Intent established `proto-go` as the managed
implementation-execution boundary of the coding-agent Development System.

It also established a durable distinction between implementation work that is
still in progress and implementation work that has satisfied its implementation
and validation obligations and is ready for downstream version-control
progression.

That initial Product Intent deliberately left open whether the ready-for-handoff
boundary is itself the normal successful terminal outcome of `proto-go`, or whether
`proto-go` owns the user-facing objective through downstream publication.

The product owner has resolved that question.

The current `proto-go` must also be product-complete on its own terms. Its
semantics must not rely on Turnlock existing today or acquiring a future control
handoff capability.

## Decision

`proto-go` is an end-to-end implementation-to-publication procedure.

The normal successful terminal outcome of `proto-go` is **publication**, not merely
implementation completion or readiness for downstream handoff.

The lifecycle therefore contains an internal durable boundary:

```text
managed authoring
→ implementation validation
→ READY FOR HANDOFF
→ downstream version-control progression
→ PUBLISHED
```

READY FOR HANDOFF is a durable internal milestone.

It means that the Development System has established that the implementation
contribution has satisfied the implementation and validation obligations
required to cross into downstream version-control progression.

It does not mean that `proto-go` has completed successfully.

`PUBLISHED` is the normal successful terminal outcome of the logical `proto-go`
operation.

For this Product Intent, publication means that the contribution has reached the
publication outcome required by the governing version-control policy.

This ADR does not further define route-specific publication semantics.

In particular, it does not decide how direct-target publication,
provider-mediated publication, pull requests, merge queues, or other publication
routes establish their route-specific published outcome.

## Downstream responsibility remains separate

`proto-go` does not absorb the internal semantics of the downstream version-control
system.

The Development System and `proto-go` own the end-to-end implementation objective.

The downstream version-control system owns the mechanical version-control
progression assigned to it.

Conceptually:

```text
proto-go
  → managed authoring
  → validation
  → durable READY boundary
  → request downstream version-control progression

downstream version-control system
  → perform the version-control transitions it owns
  → report its result
```

Using a downstream system does not make `READY FOR HANDOFF` the terminal product
outcome of `proto-go`.

## Mechanical publication failure does not automatically terminate `proto-go`

A downstream publication attempt may determine that it cannot currently make
further mechanical progress.

If the returned condition can be resolved by authored development or convergence
work within the current Development System's authority, that result does not by
itself terminate the `proto-go` objective.

The main agent may continue the same `proto-go` objective by performing the required
authored work, revalidating affected implementation work when necessary, and
retrying downstream version-control progression.

Conceptually:

```text
READY FOR HANDOFF
→ downstream publication attempt
→ mechanically blocked

main agent
→ authored correction / convergence
→ required revalidation
→ downstream publication retry

repeat as required
→ PUBLISHED
```

This ADR does not define the exact algorithm for correction, convergence,
revalidation, retry, or publication.

It defines only the product ownership and terminal-outcome semantics.

## Non-success

If the publication outcome required by the governing version-control policy has
not been reached, `proto-go` must not report successful completion.

A condition requiring an unresolved action, decision, permission, or authority
outside the current Development System may prevent `proto-go` from reaching its
successful terminal outcome.

The exact taxonomy, persistence representation, retry interface, and user-facing
representation of such non-success or blocked conditions remain undecided.

This ADR must not be used to invent those mechanisms.

## Turnlock independence

The current `proto-go` must satisfy this Product Intent without relying on
Turnlock.

No `proto-go` product guarantee established here depends on:

* Turnlock being present;
* Turnlock owning `proto-go`;
* Turnlock being able to return control to the main agent;
* a future Turnlock feature;
* a future migration of `proto-go` onto Turnlock.

Turnlock may later become one conforming execution mechanism for `proto-go` if it can
realize the already-established `proto-go` semantics.

Such a future integration would implement `proto-go` Product Intent.

It would not define it.

## Consequences

The following consequences are accepted:

1. `READY FOR HANDOFF` is not the successful terminal state of `proto-go`.

2. Successful `proto-go` completion requires the governing publication outcome to
   have been reached.

3. The ready-for-handoff fact must still be durable because publication may
   fail, execution may stop, or the originating session may disappear after
   implementation completion but before publication.

4. Downstream version-control progression remains a distinct responsibility even
   though `proto-go` composes it into its end-to-end user-facing objective.

5. A mechanically blocked downstream publication may return authored work to the
   main agent without changing `proto-go`'s end-to-end objective.

6. Work changed during authored correction or convergence must not bypass the
   validation obligations governing implementation readiness.

7. `proto-go` must not claim success merely because implementation is complete,
   validation passed, a contribution is durable, a commit exists, or a
   publication attempt was made.

8. The exact representation of lifecycle states, persistence, retry, recovery,
   blocking outcomes, and publication mechanisms remains outside this decision.

9. `proto-go` Product Intent is independent of Turnlock implementation availability.

## Non-decisions

This ADR does not decide:

* how managed authoring isolation is implemented;
* whether Git worktrees are used;
* how contribution identity is represented;
* how lifecycle state is persisted;
* how `READY FOR HANDOFF` is persisted;
* how publication state is persisted;
* which downstream version-control implementation is used;
* the API used to invoke a downstream version-control system;
* the route-specific definition of publication;
* how many publication retries are allowed;
* whether retry is represented as one process, several processes, one session,
  or several sessions;
* the exact non-success or blocked-state taxonomy;
* whether Turnlock is ever used to implement `proto-go`.

Those questions require separate derivation or explicit decisions.
