# ADR-003: Keep validation policy outside proto-go and fail closed on unknown obligations

- Status: Accepted
- Date: 2026-09-23

## Context

The Product Intent establishes that `proto-go` owns managed implementation,
validation progression, durable readiness, and the end-to-end objective through
publication.

The specification also establishes that `READY FOR HANDOFF` is an authoritative
internal lifecycle boundary.

The remaining product question was whether `proto-go` itself defines a universal
validation methodology that determines readiness, or whether readiness is
governed by validation obligations originating from the authorities governing
the implementation occurrence.

A related question was whether `proto-go` may establish readiness when it can execute
some known checks but cannot establish whether the governing validation
obligation set is complete.

The product owner has resolved both questions.

## Decision

`proto-go` does not own validation policy.

The validation obligations applicable to one logical `proto-go` implementation
occurrence come from the governing authorities applicable to that occurrence.

`proto-go` owns the operational responsibility to:

1. identify the applicable validation obligations from governing authority;
2. execute those obligations or cause them to be executed;
3. establish whether they are satisfied;
4. refuse `READY FOR HANDOFF` when the applicable governing obligation set
   cannot be established with sufficient authority.

A universal hard-coded `proto-go` checklist MUST NOT become the authority that
defines readiness.

For example, `proto-go` MUST NOT define product readiness universally as:

```text
tests
+ lint
+ typecheck
+ review
= READY FOR HANDOFF
```

unless those exact obligations are independently established by the governing
authority applicable to the implementation occurrence.

`proto-go` may use implementation techniques or additional evidence internally, but
its own methodological preference does not become product authority merely
because `proto-go` executes it.

## Readiness requires the applicable governing obligations

`READY FOR HANDOFF` requires every applicable validation obligation established
from governing authority to be satisfied.

Conceptually:

```text
governing implementation context
        ↓
applicable validation obligations
        ↓
proto-go identifies them
        ↓
proto-go executes / causes execution
        ↓
all applicable obligations satisfied
        ↓
READY FOR HANDOFF may be established
```

This ADR does not define the complete authority graph or the algorithm used to
identify obligations.

## Unknown obligation completeness fails closed

If `proto-go` cannot establish the applicable validation-obligation set with
sufficient authority, `proto-go` MUST NOT establish `READY FOR HANDOFF`.

Conceptually:

```text
known validations pass
        +
applicable obligation set cannot be established
        ↓
NOT READY
```

Passing every validation `proto-go` happened to discover is not sufficient when the
product cannot establish that the governing obligation set has been identified
with sufficient authority.

Uncertainty about governing validation completeness therefore fails closed at
the readiness boundary.

This is distinct from requiring an arbitrarily maximal validation strategy.

`proto-go` is not required by this ADR to invent additional tests or verification
methods merely because they might increase confidence.

The requirement is to satisfy the validation obligations established by
governing authority and to avoid claiming readiness when `proto-go` cannot establish
what those governing obligations are.

## Relationship to post-readiness mutation

`PROTO-GO-INV-010` already requires authored mutation after readiness not to bypass
the readiness and validation obligations applicable to the resulting work.

This ADR clarifies the source of those obligations.

When authored correction or convergence changes the contribution after an
earlier readiness boundary, `proto-go` must evaluate the resulting work against the
applicable governing validation obligations.

This ADR does not require every previously executed validation to be rerun
unconditionally.

The exact rules for invalidation, incremental validation, reuse of evidence, and
re-execution remain undecided.

## Consequences

The following consequences are accepted:

1. `proto-go` is not the product authority that decides a universal validation
   methodology.

2. Validation requirements may vary between implementation occurrences because
   their governing authorities may differ.

3. `READY FOR HANDOFF` requires satisfaction of every applicable validation
   obligation established from governing authority.

4. Successful execution of a fixed built-in checklist is insufficient by itself
   unless that checklist is established as complete for the occurrence by
   governing authority.

5. If `proto-go` cannot establish the applicable validation-obligation set with
   sufficient authority, readiness is forbidden.

6. Unknown validation completeness is therefore a fail-closed readiness
   condition.

7. `proto-go` remains responsible for operationally identifying and executing or
   causing execution of the governing obligations.

8. The existence of external validation authority does not transfer `proto-go`'s
   lifecycle responsibility to that authority.

## Non-decisions

This ADR does not decide:

* the complete set of possible validation authorities;
* the precedence order between validation authorities;
* how conflicting authorities are reconciled;
* how validation obligations are encoded;
* how `proto-go` discovers governing authorities;
* how `proto-go` discovers validation obligations;
* whether obligation discovery is static or dynamic;
* whether validation obligations may change during one logical `proto-go` operation;
* what evidence format proves an obligation satisfied;
* whether validation evidence is persisted;
* whether validation evidence can be reused;
* when validation evidence becomes stale;
* whether all validations must be rerun after authored mutation;
* incremental validation semantics;
* validation scheduling;
* validation parallelism;
* validation tooling;
* user-facing representation of unknown or unsatisfied obligations;
* implementation architecture.

Those questions require separate derivation or explicit decisions.
