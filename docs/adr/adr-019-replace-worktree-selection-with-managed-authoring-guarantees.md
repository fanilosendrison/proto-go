# ADR-019: Replace worktree selection with managed authoring guarantees

- Status: Accepted
- Date: 2026-09-27

## Context

ADR-010 selected dedicated temporary detached Git worktrees as the concrete
managed-authoring mechanism for `proto-go`. That decision was accepted and its
historical record remains unchanged.

The current product requirement is broader than that mechanism. Managed
authoring must protect the observable properties that make concurrent,
recoverable implementation safe, while remaining open to multiple future
realizations. In particular, the Product Intent must govern isolation from
unrelated mutable authoring state, independence from incidental ambient
residue, access to the complete development workspace made available by the
Development System, discovery of repository participation during authoring, and
continuity across replacement or loss of a concrete authoring environment.

Neither `PROTO-GO-INV-005` nor ADR-004 requires Git worktrees. ADR-004 already
permits repository participation to expand during authoring. ADR-013 establishes
that `PUBLISHED` remains true after a later closure failure and that normal
successful completion requires applicable proto-go-owned closure obligations.
ADR-017 establishes that these are proto-go domain semantics and that Prelock is
the generic execution substrate.

The product owner has resolved that these observable guarantees, rather than a
Git worktree, are the managed-authoring contract.

## Decision

`proto-go` requires a product-level **Managed Authoring Environment** for managed
authoring. The term identifies the managed authoring context and its required
observable properties; it does not select a VM, container, process boundary,
filesystem, Git worktree, snapshot, clone, image, cache, provider, or
provisioning mechanism.

A conforming Managed Authoring Environment must provide the following
properties:

1. **Isolation from unrelated mutable authoring state.** Managed authoring for
   one `ManagedContribution` must not accidentally observe or depend on mutable
   authoring state belonging to an unrelated `ManagedContribution`. This
   remains true when concurrent contributions concern the same repository,
   several repositories, or the same logical files.

2. **Authoritative starting state.** The relevant starting state for managed
   authoring must come from an authoritative development state. Mutable state
   intentionally belonging to the same `ManagedContribution` may be retained
   and used to continue that contribution. Incidental Mutable Residue must not
   become an implicit input or authority merely because it is present.

3. **Complete available development workspace.** Managed authoring must have
   access to the complete development workspace that the Development System
   authoritatively makes available for the work. It must not be restricted to a
   repository subset predicted from the initial task. Visibility or
   inspectability of a repository does not by itself make that repository a
   participating repository.

4. **Dynamic repository participation.** When authoring discovers that another
   repository must participate, that repository may enter the existing
   `ManagedContribution`. Before the first managed authored mutation belonging
   to the contribution occurs in that repository, the repository must enter the
   contribution's managed authoring authority. Discovery of an initially
   unpredicted repository must not by itself require abandoning or replacing
   the same `ManagedContribution`.

5. **Continuity independent of environment survival.** `ManagedContribution`
   identity, lifecycle facts, and authoritative authored state required for
   correct continuation must not depend on the survival of one concrete Managed
   Authoring Environment. Loss, destruction, replacement, or non-reuse of an
   environment must not by itself create a new contribution, lose required
   authoritative authored state, silently substitute another state, erase an
   established lifecycle fact, or change whether work is complete. The same
   mutable environment is not required to survive for the contribution's
   lifetime.

The meaning of a fresh managed-authoring start is semantic: the work begins from
an authoritative development state and is independent of incidental mutable
residue. It does not require the physical creation of a new environment. An
implementation may reuse or replace a concrete environment when the required
properties continue to hold.

The Product Intent introduces `Managed Authoring Environment`,
`Authoritative Development State`, `Incidental Mutable Residue`, and `Available
Development Workspace` as product-level concepts. None of them defines
`ManagedContribution` identity, and none prescribes a concrete representation.

The generic isolation property in `PROTO-GO-INV-005`, the lifecycle durability
property in `PROTO-GO-INV-011`, and the dynamic participation property in
`PROTO-GO-INV-016` remain the relevant existing invariant identities. They
remain in force without selecting a mechanism. The guarantees above are
Product Intent for a later complete invariant derivation; this ADR does not
create a new invariant identifier.

`PROTO-GO-INV-043`, `PROTO-GO-INV-044`, and `PROTO-GO-INV-045` are superseded.
Their identities remain historical and must not be reused. The worktree-
specific requirements they carried are replaced by the abstract Product Intent
and the existing mechanism-independent lifecycle and isolation obligations.

ADR-010 is superseded with respect to its selection of Git worktrees, detached
HEAD, determinate commits, one-worktree-per-repository bindings, and
worktree-specific lifetime and cleanup requirements. ADR-010's accepted
historical record is not rewritten. ADR-004 remains in force and is strengthened
by the complete-workspace and dynamic-participation guarantees above.

Closure remains abstract. Proto-go-owned closure obligations apply to resources
or effects that proto-go has established when their applicable lifecycle
requires retirement or cleanup. No Managed Authoring Environment is assumed to
have a removable object. A failure of an applicable closure obligation after
`PUBLISHED` does not make `PUBLISHED` false and remains actionable until the
obligation is satisfied or the applicable non-success semantics apply.

This decision does not change the Prelock boundary. Prelock remains a generic
execution-continuity and control-transfer substrate and does not acquire
knowledge of managed-authoring environments, available repositories, complete
workspaces, authoring isolation, freshness, authoritative authored state,
incidental residue, snapshots, VMs, containers, or filesystem mechanisms.

No performance requirement is introduced. No performance threshold, provisioning
latency, cost target, or optimization preference is part of this decision.

## Consequences

- Designs are evaluated against managed-authoring guarantees rather than against
  the use or absence of Git worktrees.
- A conforming design must prevent unrelated mutable residue from becoming an
  implicit authoring input, including when a concrete environment is reused.
- A managed authoring start may inspect the complete available development
  workspace and may discover repository participation progressively.
- Authoritative contribution state and lifecycle facts require a future
  representation that is independent of any replaceable authoring environment;
  this ADR does not select that representation.
- Future environment retirement or cleanup is governed only when an applicable
  proto-go-owned closure obligation exists. Existing `PUBLISHED` and completion
  semantics remain in force.
- The complete derivation of new invariants and the concrete architecture remain
  future work.

## Non-decisions

This ADR does not decide:

- VM, Spot VM, container, process, or microVM use;
- snapshot, image, immutable-image, or filesystem copy-on-write strategy;
- Git clone, worktree, branch, checkout, or another repository materialization;
- cache design or repository materialization optimization;
- workspace synchronization protocol;
- state persistence mechanism or authoritative-state storage format;
- environment provisioning system, provider, or lifecycle implementation;
- workspace manifest format;
- runtime API, workflow language, or process topology;
- performance thresholds or provisioning targets;
- the complete derived invariant set;
- Prelock Product Intent or Prelock responsibilities.
