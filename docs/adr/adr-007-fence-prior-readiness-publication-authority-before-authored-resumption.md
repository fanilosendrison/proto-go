# ADR-007: Fence prior readiness publication authority before authored resumption

- Status: Accepted
- Date: 2026-09-23

## Context

The accepted proto-go semantics establish that a `READY FOR HANDOFF`
occurrence applies to a determinate authored state of one
`ManagedContribution`.

Downstream progression for that readiness occurrence must operate on the state
bound by that occurrence rather than on an authored target that changes
underneath it.

The accepted semantics also allow downstream mechanical progression to become
blocked while the same logical proto-go objective continues through authored
correction or convergence.

When authored work changes the contribution after readiness, the previous
readiness occurrence does not authorize the resulting authored state and a new
applicable readiness occurrence is required before downstream progression of
that resulting state.

A remaining semantic gap exists between those rules.

Consider:

```text
state S1
        ↓
READY #1
        ↓
downstream progression begins
        ↓
authored correction becomes necessary
        ↓
main agent begins producing S2
```

If `READY #1` still retains effective authority to cross the governing
publication boundary while S2 is being authored, downstream progression for
`READY #1` could later publish S1 even though proto-go has already resumed
authoring toward a replacement state.

That would allow two competing states of the same `ManagedContribution` to
remain candidates for the one terminal publication outcome.

The existing semantics therefore require an explicit publication-authority
retirement and fencing boundary before authored work resumes.

## Decision

Before managed authored mutation resumes after a publication-authorizing
`READY FOR HANDOFF` occurrence, that readiness occurrence must cease to
authorize future crossing of the governing publication boundary.

Conceptually:

```text
S1
        ↓
READY #1
        ↓
downstream progression
        ↓
authored correction required
        ↓
retire READY #1 publication authority
        ↓
establish READY #1 can no longer cross publication boundary
        ↓
only then may authored mutation resume
        ↓
S2
```

This ordering is mandatory.

A local declaration that the prior readiness occurrence is obsolete is
insufficient if downstream progression already originating from that occurrence
can still later cross the governing publication boundary.

proto-go must establish the fencing property before authored mutation resumes.

## Historical readiness remains historical truth

Retiring publication authority does not retroactively invalidate the earlier
readiness decision.

After retirement, both of the following may simultaneously be true:

```text
READY #1 was validly established for S1
READY #1 no longer authorizes publication of S1
```

Therefore:

```text
historical readiness fact
!= current publication authority
```

The Product Intent does not require readiness history to be erased merely
because authored work later resumes.

## Retired readiness cannot later publish

Once a readiness occurrence has lost publication authority, downstream
progression originating from that occurrence must not subsequently cause its
bound logical publication unit to reach the governing publication outcome.

This remains true even if downstream preparation for that occurrence already
exists.

For example:

```text
READY #1
        ↓
prepare effect A
prepare effect B
effect C blocks
        ↓
publication authority retired
```

The prepared effects do not thereby have to disappear.

However, they must not retain authority capable of making the retired
readiness occurrence published.

Therefore:

```text
retire publication authority
!= necessarily destroy preparation
```

## New authored state requires new readiness

After the prior readiness occurrence is fenced and authored mutation resumes,
the resulting work remains subject to the already-established validation and
readiness requirements.

Conceptually:

```text
READY #1
        ↓
publication authority retired and fenced
        ↓
authoring resumes
        ↓
state S2
        ↓
applicable validation/readiness obligations
        ↓
READY #2
        ↓
new downstream progression
```

`READY #2` is a new readiness occurrence of the same logical
`ManagedContribution`.

The prior occurrence is not reactivated.

This remains true even if later authored work happens to produce content equal
to content previously bound by the retired readiness occurrence.

A readiness occurrence is an authoritative lifecycle occurrence, not merely a
content hash.

## Single effective publication candidate

The accepted semantics imply that one `ManagedContribution` cannot
simultaneously retain multiple readiness occurrences each capable of reaching
the governing publication outcome.

At any point in the proto-go lifecycle, no more than one readiness occurrence
for the `ManagedContribution` may retain effective authority to cross the
governing publication boundary.

This is a consequence of the invariants established by this ADR and is not a
separate invariant identity.

## Fencing is semantic, not architectural

This ADR requires the following property:

```text
old readiness publication authority retired
        ↓
old readiness can no longer cross governing publication boundary
        ↓
authored mutation may resume
```

It does not select the implementation mechanism that provides that property.

In particular, this ADR does not select:

```text
locks
leases
generations
tokens
compare-and-swap
cancellation protocols
Git refs
process ownership
database transactions
downstream acknowledgements
```

Those remain architectural questions.

## Relationship to existing invariants

This ADR does not supersede any currently normative invariant.

It closes the transition between:

* `PROTO-GO-INV-018`, which requires later authored work to obtain new
  applicable readiness;
* `PROTO-GO-INV-019`, which requires downstream progression to operate on the
  state bound by its readiness occurrence; and
* `PROTO-GO-INV-008`, which establishes one governing publication outcome as
  the normal successful terminal outcome.

It is also compatible with the indivisible publication semantics established
by `PROTO-GO-INV-020` through `PROTO-GO-INV-022`.

`PROTO-GO-INV-012` remains superseded by ADR-006 and is unaffected by this
decision.

## Consequences

The following consequences are accepted:

1. Authored resumption after readiness requires retirement of the prior
   readiness occurrence's publication authority.

2. Publication-authority retirement occurs before the first subsequent managed
   authored mutation.

3. proto-go must establish that the retired readiness occurrence can no longer
   cross the governing publication boundary before authored mutation resumes.

4. A retired readiness occurrence cannot later become published.

5. Historical readiness remains historically valid after publication authority
   is retired.

6. Preparatory downstream artifacts do not necessarily need to be destroyed
   when publication authority is retired.

7. Preparatory artifacts belonging to retired readiness must not retain
   effective publication authority.

8. A later eligible authored state requires a new readiness occurrence.

9. A retired readiness occurrence is not reactivated even if later work returns
   to equivalent content.

10. At most one readiness occurrence of a `ManagedContribution` may retain
    effective authority to reach governing publication at a time.

## Non-decisions

This ADR does not decide:

* how publication authority is represented;
* whether publication authority is persisted as a distinct field;
* whether readiness occurrences have generations;
* whether fencing uses generations;
* whether fencing uses tokens;
* whether fencing uses leases;
* whether fencing uses locks;
* whether fencing uses compare-and-swap;
* whether fencing uses cancellation;
* whether fencing uses Git refs;
* whether fencing uses process termination;
* whether fencing requires downstream acknowledgement;
* whether a database participates in fencing;
* whether preparatory artifacts are deleted;
* whether preparatory artifacts may later be reused;
* garbage-collection semantics;
* downstream-process lifetime;
* retry representation;
* recovery mechanisms;
* readiness-occurrence persistence;
* publication-authority persistence;
* downstream implementation.

Those questions require separate derivation or architectural decisions.
