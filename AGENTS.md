---
okf_version: "1.0"
kind: "KnowledgeAsset"
asset_type: "agent-directives"
domain: "proto-go"
severity: "strict"
name: "proto-go repository agent directives"
---

# proto-go repository directives

Use this file as the operational map for the `proto-go` repository.

The repository is currently specification-first.

Do not infer implementation architecture from the repository name, from the old
`/go`, or from adjacent projects.

proto-runtime and proto-ruu are adjacent products, not proto-go semantic
authority. Consult them as context; do not let them redefine proto-go Product
Intent.

## Product boundary

`proto-go` is currently defined by the Product Intent in:

```text
docs/specification/proto-go-spec.md
```

The surrounding coding harness owns policy about whether implementation is
permitted outside `proto-go`.

That policy is not part of `proto-go` Product Intent.

Do not add a product invariant stating that `proto-go` is the only way code may be
modified merely because the current harness enforces such a rule externally.

ADR-001 establishes a separate product boundary:

```text
READY FOR HANDOFF
≠ successful proto-go completion

governing publication outcome reached
+ all applicable proto-go-owned closure obligations satisfied
= normal successful proto-go completion
```

ADR-002 establishes that:

```text
one logical proto-go operation
→ exactly one ManagedContribution

one ManagedContribution
→ one or more repositories

ManagedContribution
!= Repository
```

Do not define contribution identity in terms of repository, branch, worktree,
session, agent, process, or mutable authoring-surface identity.

The currently admitted normative invariant identifiers are:

```text
PROTO-GO-INV-001 through PROTO-GO-INV-065
```

Superseded invariants are historical identities and are no longer normative:

```text
PROTO-GO-INV-008   superseded by ADR-013
PROTO-GO-INV-012   superseded by ADR-006
PROTO-GO-INV-020   superseded by ADR-014
PROTO-GO-INV-021   superseded by ADR-014
PROTO-GO-INV-022   superseded by ADR-014
PROTO-GO-INV-023   superseded by ADR-017
PROTO-GO-INV-024   superseded by ADR-008
PROTO-GO-INV-025   superseded by ADR-017
PROTO-GO-INV-030   superseded by ADR-009
PROTO-GO-INV-032   superseded by ADR-009
PROTO-GO-INV-033   superseded by ADR-017
PROTO-GO-INV-034   superseded by ADR-017
PROTO-GO-INV-035   superseded by ADR-017
PROTO-GO-INV-036   superseded by ADR-017
PROTO-GO-INV-037   superseded by ADR-017
PROTO-GO-INV-038   superseded by ADR-017
PROTO-GO-INV-041   superseded by ADR-013
PROTO-GO-INV-042   superseded by ADR-017
PROTO-GO-INV-046   superseded by ADR-017
PROTO-GO-INV-048   superseded by ADR-017
PROTO-GO-INV-049   superseded by ADR-017
PROTO-GO-INV-050   superseded by ADR-017
PROTO-GO-INV-052   superseded by ADR-017
```

Amended by ADR-017 without changing their semantic property:

```text
PROTO-GO-INV-031   Launch Contract authority root
PROTO-GO-INV-039   Admission requires a Launch Contract
PROTO-GO-INV-040   /go re-entry continues an existing proto-go objective
PROTO-GO-INV-045   managed worktree bindings follow the contribution lifecycle
PROTO-GO-INV-047   distinct proto-go progressions may advance concurrently
```

Do not create invariants beyond `PROTO-GO-INV-065` or alter the meaning, order,
or identifiers of the admitted invariants without explicit semantic authority.

ADR-003 establishes the validation-authority boundary:

```text
governing authorities
→ define applicable validation obligations

proto-go
→ identifies applicable obligations
→ executes / causes their execution
→ establishes satisfaction
→ may establish READY only when the governing obligation set is sufficiently known
```

Do not introduce a universal `proto-go` validation checklist as normative authority.

Do not infer that a passing test suite, lint run, typecheck, review, or other
fixed set of checks is sufficient for readiness unless governing authority
establishes that obligation set for the implementation occurrence.

If the applicable governing validation-obligation set cannot be established
with sufficient authority, do not establish `READY FOR HANDOFF`.

The currently admitted invariant identifier space ends at `PROTO-GO-INV-065`.

Do not create or imply a later invariant without explicit semantic authority.

ADR-004 establishes:

```text
ManagedContribution repository scope
→ may expand during authoring

new repository
→ must enter managed authoring authority
→ before first managed mutation
```

A readiness occurrence applies to a determinate authored state and repository
participation.

Do not treat `READY FOR HANDOFF` as a permanent boolean authorizing future
mutations.

ADR-005 established one indivisible logical publication unit. ADR-014
supersedes that all-or-none cross-repository publication-visibility semantics.

Current semantics:

```text
one READY occurrence
→ aggregate Repository Publication Obligations

repository-local obligations
→ may be satisfied independently

proper subset satisfied
→ ManagedContribution NOT PUBLISHED

all obligations of effective READY satisfied
→ PUBLISHED may be established
```

The currently admitted invariant identifier space ends at `PROTO-GO-INV-065`.

Do not create or imply a later invariant without explicit semantic authority.

ADR-006 established the superseded proto-go execution model. ADR-017
supersedes main-agent-skill orchestration and terminating proto-go script
execution as proto-go Product Intent.

`/go` remains the user-facing invocation. The proto-go workflow owns domain
semantics and progression decisions; proto-runtime provides generic execution
continuity and control transfer.

`PROTO-GO-INV-012`, `PROTO-GO-INV-023`, `PROTO-GO-INV-024`, and
`PROTO-GO-INV-025` are historical superseded identities.

ADR-007 establishes the authored-resumption publication fence, as narrowed by
ADR-015:

```text
publication-authorizing READY
          ↓
authored correction required
          ↓
retire prior READY global completion authority
          ↓
prohibit new repository publication initiation under prior READY
          ↓
only then resume authored mutation
```

Already-originated repository-local publication may still complete after
retirement; it must not be cancelled or fenced as a precondition to authored
resumption.

A retired READY cannot establish ManagedContribution PUBLISHED, cannot initiate
new publication work, and is never reactivated.

Retirement does not erase the historical readiness fact.

A later READY evaluates its own publication obligations against current
authoritative publication reality.

Do not select a fencing implementation mechanism from this semantic rule.

Do not invent distributed atomicity, rollback, or mandatory cancellation.

The currently admitted invariant identifier space ends at
`PROTO-GO-INV-065`.

Do not create or imply a later invariant without explicit semantic authority.

ADR-008 established Invocation Preflight, Admission, the Launch Contract as the
initial authority root, and fail-closed Admission.

ADR-009 established state/authority-driven progression, pre-Admission
progression, re-entry continuing the same objective, READY as intermediate, and
progression through publication.

ADR-017 preserves those business boundaries and supersedes the proto-go script,
Continuation Artifact, Continuation Policy, Progression Context, fresh
invocation, and main-agent/script control-transfer machinery.

Current business ordering is:

```text
/go
→ Invocation Preflight
→ pre-Admission proto-go workflow progression
→ admission-complete Launch Contract
→ Admission
→ proto-go workflow progression
→ READY (intermediate)
→ version-control progression through proto-ruu as applicable
→ continued progression / correction / revalidation / new READY
→ PUBLISHED
→ applicable proto-go-owned closure obligations
→ normal successful completion
```

Do not require Admission completeness before pre-Admission execution.

Do not depend on main-agent conversational memory for proto-go business state;
execution continuity is provided by proto-runtime.

Do not give the execution substrate authority over proto-go domain decisions.

Do not equate Launch Contract with one concrete serialization or invocation
format.

Do not invent authority missing from the Launch Contract or later authorized
authority resolution.

Do not create a new logical objective merely because an outstanding workflow
execution is re-entered through another /go invocation.

Do not treat READY FOR HANDOFF as normal proto-go completion.

Do not invent a fixed universal stage pipeline.

ADR-011 established concurrent, session-agnostic progression. ADR-017
preserves the user-visible domain consequences:

```text
distinct proto-go operations may coexist and progress concurrently
proto-go business identity is not owned by a conversational session
a session change creates no new objective, ManagedContribution, or worktree
```

Do not require global serialization of otherwise-independent proto-go
progressions.

Generic controller coordination, session transfer, and execution re-entry are
provided by proto-runtime.

ADR-012 required incremental /go instruction loading. ADR-017 supersedes that
requirement as proto-go Product Intent; instruction loading and execution
continuity belong to proto-runtime and the surrounding harness.

ADR-013 established actionable-condition continuation and terminal semantics.
ADR-017 preserves the semantic core:

```text
actionable condition
+ authorized progress remains possible
→ same proto-go objective remains in force

PUBLISHED
→ required for normal successful completion
→ not sufficient while proto-go-owned closure obligations remain
```

Do not allow an actionable condition to be silently terminal when authorized
progress toward the same objective remains possible.

Do not revert an established `PUBLISHED` fact because a later local cleanup
failed.

Do not report normal successful completion while applicable proto-go-owned
closure obligations, including managed-worktree cleanup, remain unsatisfied.

ADR-016 makes the procedure locally evolvable and explicitly composable:

```text
procedural change with local semantic effect
→ changes proportional to that effect

effective procedure
→ explicitly identifiable composition of independently
  understandable procedural responsibilities
→ not emergent from distributed orchestration plumbing
```

Do not let execution plumbing implicitly define the procedure.

Do not require a procedural responsibility to know the complete end-to-end
procedure merely to participate in it.

Do not require unrelated procedural components to change solely to route
control through one procedural capability change.

Do not let incidental orchestration mechanics create global coupling.

Do not turn proto-go into a generic workflow engine, DSL, scheduler,
orchestration framework, or Turnlock substitute.

Do not select a procedural composition representation, step abstraction,
workflow graph, state-machine representation, dispatcher, plugin interface,
module boundary, or workflow language from ADR-016; those remain future
derivation work.

ADR-017 delegates generic workflow execution to proto-runtime:

```text
proto-go
= implementation-to-publication workflow
= owns domain semantics and progression decisions

proto-runtime
= reusable execution substrate
= owns generic execution continuity and control-transfer mechanics

proto-go decides WHAT progression is semantically required
proto-runtime realizes HOW execution continues
```

Do not re-implement generic workflow-runtime mechanisms inside proto-go.

Do not let the execution substrate decide proto-go domain transitions.

Do not absorb proto-ruu internal Git semantics into proto-go.

Do not turn proto-go into a runtime, proto-runtime into proto-go domain logic,
or proto-ruu into the owner of proto-go publication semantics.

Do not select a runtime API, workflow language, persistence mechanism, or
process topology from ADR-017; those remain future derivation work.

## Authority by responsibility

1. `docs/specification/proto-go-spec.md`
   defines current normative product meaning, canonical terminology, and stable
   `PROTO-GO-INV-NNN` invariant identities.

2. Accepted ADRs under `docs/adr/`
   record explicit decisions and amendments.

3. `docs/vision/proto-go-vision.md`
   is non-normative.

4. `docs/repository-governance/`
   governs repository procedure only.

5. Future formal artifacts may check accepted semantics for their declared
   scope but do not replace normative Product Intent.

6. Future implementation and tests must conform to accepted authority and must
   not create missing product semantics.

Report inconsistencies between authoritative sources.

Do not silently choose the interpretation most convenient for implementation.

## Required reading

Before changing product semantics, deriving architecture, or preparing
implementation work, read:

1. `README.md`
2. `docs/specification/proto-go-spec.md`
3. `docs/adr/README.md`
4. `docs/adr/index.md`
5. `docs/repository-governance/proto-go-discovery-classification.md`
6. `docs/repository-governance/proto-go-engineering.md`

## Discovery handling

Every material discovery that affects product meaning must follow:

```text
docs/repository-governance/proto-go-discovery-classification.md
```

A missing semantic decision is not permission to improvise.

## Current implementation prohibition

At the current repository state, do not create:

```text
src/
bin/
tests/
formal/
qualification/
scripts/
package manifests
runtime configuration
database schemas
worktree registries
public APIs
```

unless a later explicit task is backed by sufficient accepted upstream
authority.

Do not select a programming language.

Do not select a persistence engine.

Git worktree authoring isolation is product-mandated.

Managed authored mutation must occur in the proto-go-managed detached worktree,
not in the invoking or current ordinary checkout.

Do not invent unspecified details such as worktree filesystem path, naming,
registry representation, base-commit selection algorithm, reconstruction
mechanism, forced-removal policy, or garbage collection.

Do not select a registry representation.

Do not select a concrete Turnlock workflow.

Do not select GCP or Ruu integration mechanics.

Do not implement proto-runtime, proto-ruu, or generic workflow execution inside
proto-go.

Do not select a proto-runtime API, workflow language, workflow artifact syntax,
persistence model, execution-status vocabulary, or process topology.

## ADR discipline

Do not create an ADR speculatively.

An ADR requires an actual identified decision.

A semantic ADR requires explicit product-owner resolution.

An architecture ADR requires sufficient upstream semantics to constrain the
decision.

## Repository naming

Use lowercase kebab-case for ordinary new files and directories except standard
entry points such as:

```text
AGENTS.md
README.md
```

The repository directory name `proto-go` does not select the Go programming language.

## Current target structure

The current authorized tree is:

```text
proto-go/
├── .gitignore
├── AGENTS.md
├── README.md
└── docs/
    ├── adr/
    │   ├── README.md
    │   └── index.md
    ├── repository-governance/
    │   ├── proto-go-discovery-classification.md
    │   └── proto-go-engineering.md
    ├── specification/
    │   └── proto-go-spec.md
    └── vision/
        └── proto-go-vision.md
```

Do not expand this structure merely because a likely future directory can be
anticipated.
