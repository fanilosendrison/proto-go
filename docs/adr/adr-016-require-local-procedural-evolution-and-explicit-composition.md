# ADR-016: Require local procedural evolution and explicit procedural composition

- Status: Accepted
- Date: 2026-09-24

## Context

The current proto-go procedure and its orchestration semantics are distributed
across the `/go` skill, main-agent control, terminating proto-go script
invocations, Continuation Artifacts, Continuation Policy, Progression Context,
session-transfer semantics, lifecycle rules, and multiple accepted ADRs and
invariants.

`proto-go` is a temporary implementation-to-publication system used before the
final Go + Turnlock system is available. It is not responsible for discovering
what Go should become, and Turnlock already owns the long-term generic
workflow-runtime problem.

The product problem addressed here is narrower.

Future procedural changes currently risk requiring unrelated changes across the
rest of the procedure merely because of how `proto-go` currently transfers
control between the main agent, mechanical execution, sessions, or other
execution mechanisms.

The product owner has resolved the required product property.

## Decision

`proto-go`'s implementation-to-publication procedure must support local
procedural evolution.

A procedural change whose semantic effect is local must require changes
proportional to that semantic effect, rather than unrelated changes across the
rest of the procedure merely because of how `proto-go` currently transfers
control between the main agent, mechanical execution, sessions, or other
execution mechanisms.

The effective procedure must remain explicitly identifiable as a composition of
independently understandable procedural responsibilities rather than emerging
implicitly from distributed orchestration plumbing.

The following consequences are normative:

* execution plumbing must not itself implicitly define the procedure;
* a procedural responsibility must not need unrelated knowledge of the complete
  end-to-end procedure merely to participate in it;
* adding, removing, replacing, reordering, or refining one procedural capability
  must not require unrelated procedural components to change solely to route
  control through that change;
* changing how a procedural capability is executed or continued must not
  unnecessarily redefine surrounding procedure semantics;
* genuinely global semantic constraints may legitimately couple multiple
  procedural responsibilities;
* incidental orchestration mechanics must not create equivalent global
  coupling;
* this requirement does not require `proto-go` to become a general-purpose
  workflow runtime or to reproduce Turnlock capabilities.

Explicit identifiability concerns how the effective procedure is defined and
composed. It does not require the main agent to load the complete procedural
instruction corpus before progression begins; the incremental
instruction-loading semantics established by ADR-012 remain normative.

## Relationship to existing decisions

This ADR supersedes no accepted invariant.

ADR-006's main-agent skill orchestration, ADR-008's Launch Contract and
artifact-driven continuation, ADR-009's pre-Admission progression and
Progression Context, ADR-011's session-agnostic single-controller progression,
ADR-012's incremental instruction loading, and ADR-013's actionable-condition
continuation remain normative.

This decision constrains how those responsibilities and their dependencies may
evolve. It does not relocate their authority or redefine their semantics.

ADR-009 already rejected a fixed universal stage pipeline and required
state/authority-based progression. ADR-012 already required that unrelated
instruction material not be required upfront. This ADR generalizes that
direction into a product requirement on procedural locality and explicit
composition.

`PROTO-GO-INV-042` requires sufficient authoritative Progression Context at
each control transfer. That requirement concerns correctness of continuation,
not complete end-to-end procedural knowledge. Distinguishing semantically
necessary context from incidental orchestration coupling remains future
derivation work.

## Open questions exposed

This decision does not answer, and implementation must not silently answer:

* where the effective `proto-go` procedure is defined;
* what its independently understandable procedural responsibilities are;
* which dependencies between them are semantically necessary;
* which dependencies exist only because of current orchestration plumbing;
* what minimum explicit composition model is required to make local procedural
  changes local.

## Consequences

The following consequences are accepted:

1. Execution plumbing must not implicitly define the procedure.

2. A procedural responsibility must be independently understandable enough to
   participate without unrelated end-to-end procedural knowledge.

3. Adding, removing, replacing, reordering, or refining one procedural
   capability must not force unrelated procedural components to change solely
   to route control through that change.

4. Changing how a procedural capability is executed or continued must not
   unnecessarily redefine surrounding procedure semantics.

5. Global semantic constraints may legitimately couple procedural
   responsibilities; incidental orchestration mechanics may not create
   equivalent global coupling.

6. `proto-go` must not become a general-purpose workflow runtime or reproduce
   Turnlock capabilities merely to satisfy this decision.

7. This decision does not introduce or supersede any invariant identity.
   Whether the Product Intent requires new invariant identities remains future
   derivation work.

## Non-decisions

This ADR does not decide:

* a step abstraction;
* a workflow graph;
* a state-machine representation;
* a DSL;
* a central dispatcher;
* a new Continuation Policy architecture;
* plugin interfaces;
* module boundaries;
* how script invocations are factored;
* whether the procedure is represented as stages, nodes, transitions,
  obligations, rules, capabilities, or another abstraction.

Those are downstream derivations that this Product Intent must force rather
than mechanisms embedded in this decision.
