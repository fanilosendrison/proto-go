# ADR-013: Continue progression through actionable problems and terminal cleanup

- Status: Accepted
- Date: 2026-09-23

## Context

ADR-009 established artifact-driven continuation between terminating proto-go
script invocations and authorized main-agent continuations.

ADR-001 and `PROTO-GO-INV-009` established that a mechanically blocked
publication preserves the same logical proto-go objective when the required next
work remains within the Development System's authored authority.

That rule was publication-specific.

A more general rule is required for any actionable mechanical problem or
unsatisfied proto-go-owned obligation. The terminal semantics also need
correction: an established publication outcome and full proto-go completion are
not the same thing.

The product owner has resolved both questions.

## Decision

Whenever a terminating proto-go script invocation establishes a mechanical
problem, unsatisfied proto-go-owned obligation, or other continuation condition
that prevents normal completion, it must return sufficient machine-readable
Continuation Artifact information for the main agent to understand the condition
under the available Progression Context.

If an authorized main-agent continuation exists that may make progress toward
resolving that condition, the same proto-go objective must remain in force.

The script terminates.

The `/go` Continuation Policy determines the authorized continuation.

The main agent receives an opportunity to perform that continuation.

A later mechanical retry is a fresh proto-go script invocation.

Conceptually:

```text
script
↓
mechanical progression
↓
problem / unsatisfied condition established
↓
Continuation Artifact
↓
script terminates
↓
/go Continuation Policy
↓
authorized main-agent continuation exists?
        │
        ├── yes
        │     ↓
        │  main agent attempts resolution
        │     ↓
        │  fresh script invocation
        │     ↺
        │
        └── no
              ↓
        blocked / external authority / other
        non-success semantics as applicable
```

This rule is general and is not limited to publication conflicts.

Examples may include:

```text
managed-worktree provisioning problem
validation-execution problem
mechanical Git problem
publication blockage
post-publication worktree cleanup failure
```

This ADR does not invent a complete blocked-state taxonomy.

## Terminal semantics

An established governing publication outcome is an authoritative historical
fact:

```text
PUBLISHED = true
```

A later local cleanup failure must not make publication false.

However, reaching `PUBLISHED` does not necessarily mean that proto-go has no
remaining work.

Normal successful proto-go completion requires both:

```text
1. governing publication outcome established
2. all applicable proto-go-owned closure obligations satisfied
```

Therefore:

```text
PUBLISHED
is necessary for normal successful completion

PUBLISHED
is not by itself sufficient when proto-go-owned closure obligations remain
```

Worktree cleanup established by ADR-010 is one such proto-go-owned closure
obligation.

Example:

```text
publication succeeds
↓
PUBLISHED established
↓
managed worktree removal attempted
↓
removal fails
↓
Continuation Artifact
↓
script terminates
↓
main agent receives condition
↓
authorized repair
↓
fresh script invocation
↓
cleanup retry
↓
all applicable closure obligations satisfied
↓
normal successful completion
```

No canonical lifecycle states named `CLOSING`, `COMPLETE`, or similar are
introduced. The product requirement is about facts and completion conditions,
not a required concrete state-machine representation.

## Relationship to ADR-001

ADR-001 remains historical and accepted.

Its assertion that `PUBLISHED` itself is necessarily the normal terminal point
is superseded by this ADR.

The preserved part of ADR-001 is that publication is mandatory for successful
completion and that `READY FOR HANDOFF` is insufficient.

## Relationship to ADR-009

ADR-009 remains historical and accepted.

Its assertion that artifact-driven progression necessarily terminates at the
publication outcome is superseded by this ADR.

Its pre-Admission progression, Progression Context, fresh terminating
invocation, re-entry, READY, fencing, and other unaffected semantics remain
normative.

## Superseded invariants

ADR-013 supersedes:

```text
PROTO-GO-INV-008
PROTO-GO-INV-041
```

only with respect to their terminality semantics.

Their identifiers remain historical and must not be reused.

`PROTO-GO-INV-009` remains normative and becomes a publication-specific
instance of the more general actionable-condition rule.

## Non-decisions

This ADR does not decide:

* a complete blocked-state taxonomy;
* retry representation;
* a closure-obligation registry;
* cleanup command implementation;
* lifecycle state representation.

Those remain architectural or separately derived decisions.
