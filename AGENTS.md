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
PROTO-GO-INV-001 through PROTO-GO-INV-042
```

`PROTO-GO-INV-012` is superseded by ADR-006 and is no longer normative.

`PROTO-GO-INV-024` is superseded by ADR-008 and is no longer normative.

`PROTO-GO-INV-030` is superseded by ADR-009 and is no longer normative.

`PROTO-GO-INV-032` is superseded by ADR-009 and is no longer normative.

`PROTO-GO-INV-025` remains normative.

`PROTO-GO-INV-023` and `PROTO-GO-INV-025` remain normative execution-model
invariants.

`PROTO-GO-INV-024` is retained only as superseded invariant history under
ADR-008.

Do not create invariants beyond `PROTO-GO-INV-042` or alter the meaning, order, or
identifiers of the admitted invariants without explicit semantic authority.

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

The currently admitted invariant identifier space ends at `PROTO-GO-INV-042`.

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

ADR-005 establishes:

```text
one READY FOR HANDOFF occurrence
→ one logical publication unit

proper subset published independently
→ forbidden
```

Progressive preparation is permitted only when the preparatory effects do not
themselves constitute governing publication outcomes for a proper subset.

Do not choose an atomic-publication implementation mechanism from this rule.

The currently admitted invariant identifier space ends at `PROTO-GO-INV-042`.

Do not create or imply a later invariant without explicit semantic authority.

ADR-006 establishes the proto-go execution boundary:

```text
user invokes /go in main-agent session
        ↓
/go skill governs main agent
        ↓
first mechanical transition after Admission invokes proto-go script
        ↓
script runs to completion
        ↓
script returns outputs
        ↓
main agent continues proto-go procedure
```

Do not model the proto-go script as the end-to-end workflow orchestrator.

Do not design a script step that suspends into main-agent authored work and then
resumes the same script invocation.

`PROTO-GO-INV-012` is superseded and no longer normative.

`PROTO-GO-INV-023` and `PROTO-GO-INV-025` remain normative execution-model
invariants.

`PROTO-GO-INV-024` is retained only as superseded invariant history under
ADR-008.

ADR-007 establishes the authored-resumption publication fence:

```text
publication-authorizing READY
          ↓
authored correction required
          ↓
retire publication authority
          ↓
establish old READY cannot cross publication boundary
          ↓
only then resume authored mutation
```

Do not allow an old readiness occurrence to remain capable of publication while
new authored work is being produced for the same `ManagedContribution`.

Retirement does not erase the historical readiness fact.

Do not select a fencing implementation mechanism from this semantic rule.

The currently admitted invariant identifier space ends at
`PROTO-GO-INV-042`.

Do not create or imply a later invariant without explicit semantic authority.

ADR-008 established:

```text
fresh terminating script invocation
→ Continuation Artifact
→ script termination
→ /go Continuation Policy
→ authorized main-agent continuation
→ optional fresh script invocation
```

ADR-009 supersedes ADR-008's pre-Admission ordering.

Current execution ordering is:

```text
/go
→ Invocation Preflight
→ pre-Admission artifact-driven progression
→ admission-complete Launch Contract
→ Admission
→ fresh post-Admission script invocation
→ continued artifact-driven progression
→ PUBLISHED
```

Do not model Continuation Artifacts as arbitrary commands from the script.

Do not give the script procedural authority over the main agent.

Do not perform a main-agent continuation while the originating script
invocation is active.

Do not model a later script invocation as resumption of an earlier invocation.

Do not equate Launch Contract with one concrete serialization or script-argument
format.

Do not invent authority missing from the Launch Contract or later authorized
authority resolution.

ADR-009 extends artifact-driven progression across Admission and publication:

```text
/go
→ pre-Admission artifact-driven progression
→ admission-complete Launch Contract
→ Admission
→ artifact-driven progression
→ READY (intermediate)
→ continued progression / correction / new READY
→ PUBLISHED (normal success)
```

Do not require Admission completeness before every pre-Admission script
invocation.

Do not depend on main-agent conversational memory to determine the current
proto-go progression.

Do not depend on a prior script process surviving to recover progression state.

At every control transfer, ensure sufficient authoritative Progression Context
is available or resolvable for the receiving actor.

Do not equate Progression Context with a particular file, database, event log,
snapshot, artifact chain, or workflow-engine state.

Do not create a new logical objective merely because an outstanding
continuation is re-entered through another /go invocation.

Do not treat READY FOR HANDOFF as normal proto-go completion.

Do not invent a fixed universal stage pipeline.

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

Do not select a worktree topology.

Do not select a registry representation.

Do not select a concrete Turnlock workflow.

Do not select GCP or Ruu integration mechanics.

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
