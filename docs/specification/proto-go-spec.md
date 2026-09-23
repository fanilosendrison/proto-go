# proto-go Specification

> Working product specification derived from the current product discussion.
>
> This document intentionally starts from product intent before fixing
> implementation mechanisms. Terms such as worktree, persistence engine,
> registry, database, process model, agent topology, harness API, version-control
> adapter, programming language, and runtime are deliberately left unspecified
> unless the product contract later requires them.

# 0. Product intent — governing user experience

This section is normative for the current product direction.

It states the product outcome that lower-level design exists to serve.

ADR-001 establishes publication as the normal successful terminal outcome of
`proto-go` and makes `READY FOR HANDOFF` an internal durable lifecycle boundary.

ADR-002 establishes that one logical `proto-go` operation owns one
`ManagedContribution` and that one `ManagedContribution` may span one or more
repositories without repository boundaries determining contribution identity.

ADR-003 establishes that validation policy remains outside `proto-go`, that
`READY FOR HANDOFF` requires satisfaction of every applicable validation
obligation established from governing authority, and that inability to establish
the applicable obligation set with sufficient authority forbids readiness.

ADR-004 establishes that the repository scope of a `ManagedContribution` may
expand during authoring, provided each newly included repository enters the
contribution's managed authoring authority before its first managed authoring
mutation. It also establishes the resulting readiness-occurrence semantics: each
readiness occurrence applies to a determinate authored state and repository
participation.

ADR-005 establishes that the managed effects bound by one `READY FOR HANDOFF`
occurrence form one indivisible logical publication unit with all-or-none
governing publication semantics.

ADR-006 establishes proto-go's execution model: a user invokes `/go` from a
main-agent session, the `/go` skill governs the main agent's procedure, and the
main agent continues only after a terminating proto-go script invocation
returns. ADR-006 originally recorded that script invocation as the first
procedural step; ADR-008 supersedes that ordering. ADR-006 supersedes the
execution-engine-independence assertion previously recorded as
`PROTO-GO-INV-012`.

ADR-007 establishes that a readiness occurrence must lose effective publication
authority and be fenced from crossing the governing publication boundary before
managed authored mutation resumes. Historical readiness remains valid, but a
retired readiness occurrence cannot later publish.

ADR-008 establishes Invocation Preflight, Admission, Launch Contract authority,
terminating script invocations, and artifact-driven main-agent continuation.
ADR-008 originally placed Admission before mechanical script execution;
ADR-009 supersedes that pre-Admission ordering. ADR-008 supersedes
PROTO-GO-INV-024 while preserving PROTO-GO-INV-025.

ADR-009 corrects ADR-008's pre-Admission ordering by allowing Invocation
Preflight itself to use terminating proto-go script invocations and
artifact-driven main-agent continuations before an admission-complete Launch
Contract exists. It establishes `/go` re-entry into outstanding continuations,
extends artifact-driven progression through the governing publication outcome,
and requires sufficient machine-readable authoritative Progression Context at
every main-agent/script control-transfer boundary. ADR-009 supersedes
PROTO-GO-INV-030 and PROTO-GO-INV-032.

The current repository intentionally does not yet derive the complete invariant
set or architecture from this Product Intent. Those derivations must occur
explicitly rather than being invented during implementation.

`proto-go` operates inside a coding-agent development system. Policies controlling
whether implementation is permitted to occur outside `proto-go` belong to the
surrounding harness and are not part of `proto-go` product semantics.

## 0.1 Product definition: managed implementation-to-publication execution inside a coding-agent session

`proto-go` is the **end-to-end implementation-to-publication procedure of the
coding-agent Development System**.

One logical `proto-go` operation owns exactly one `ManagedContribution`.

A `ManagedContribution` represents the logical implementation occurrence, not a
repository-local change.

It may produce managed authoring effects in one repository or across several
repositories while remaining one logical contribution.

Therefore:

```text
ManagedContribution != Repository
```

Repository boundaries do not determine contribution identity.

It exists so that, once the user and Development System have determined what
should be implemented, that implementation can be carried through managed
authoring, validation, durable readiness, and publication without requiring the
user to manually provision or coordinate the mutable development environment or
manually bridge the normal implementation-to-publication lifecycle.

A `proto-go` operation owns the user-facing implementation objective from the
beginning of managed authoring until the contribution either:

- reaches the publication outcome required by the governing version-control
  policy; or
- cannot reach that outcome because further progress requires unresolved action,
  decision, permission, or authority outside the current Development System.

Implementation completion is an internal lifecycle boundary, not the normal
successful terminal outcome of `proto-go`.

After implementation and validation obligations have been satisfied, `proto-go`
establishes the contribution durably as ready for downstream version-control
progression.

The downstream version-control system retains authority over the mechanical
version-control transitions it owns. `proto-go` composes that specialized progression
into its end-to-end objective without absorbing the downstream system's internal
semantics.

The intended dependency direction is:

```text
user / Development System intent
        ↓
proto-go
managed implementation production
        ↓
implementation validation
        ↓
durable READY FOR HANDOFF boundary
        ↓
downstream version-control progression
        ↓
publication outcome required by governing policy
        ↓
successful proto-go completion
```

A downstream mechanical inability to progress does not by itself terminate the
`proto-go` objective when the current Development System can perform the required
authored correction or convergence work.

In that case, the main agent may perform the required authored work, revalidate
affected implementation work when necessary, and retry downstream progression.

## 0.2 The product promise

`proto-go` exists so that **the user can ask the coding system to implement and
publish a defined piece of work without manually managing the isolation,
lifecycle, rediscovery, or normal implementation-to-publication progression of
the resulting contribution**.

The target experience is:

```text
User and main agent determine what should be implemented.

User invokes:

  /go

The implementation proceeds in an isolated managed authoring context.

Other implementation work may proceed concurrently elsewhere.

The implementation and required validation complete.

The contribution becomes durably READY FOR HANDOFF.

The downstream version-control system attempts the progression it owns.

If mechanical publication requires authored correction or convergence:
  the main agent performs that work inside the continuing proto-go objective,
  affected implementation work is revalidated as required,
  and downstream publication is retried.

When the governing publication outcome is reached:
  proto-go succeeds.

Incomplete work is not mistaken for completed work.
Ready work is not mistaken for published work.
```

The governing promise is:

> **A `proto-go` operation turns one implementation request into exactly one
> `ManagedContribution` and carries the user-facing objective through
> publication. That `ManagedContribution` represents one logical implementation
> occurrence and may contain managed authoring effects in one or more repositories
> without repository boundaries determining its identity. `proto-go` establishes the conditions required for that contribution
> to be authored without sharing mutable implementation state with unrelated
> concurrent work, owns its implementation lifecycle, determines when the
> contribution has satisfied the Development System's implementation and
> validation requirements, durably establishes readiness before downstream
> progression, invokes the specialized version-control progression required to
> publish it, and does not report successful completion until the publication
> outcome required by governing version-control policy has been reached.
> Mechanical inability of the downstream system to progress does not by itself
> terminate `proto-go` when the Development System can perform the required authored
> work and retry. Work that has not reached the readiness boundary remains
> distinguishable from ready work, and ready work remains distinguishable from
> published work.**

## 0.3 Implementation plumbing is not a user workflow

Ordinary `proto-go` use must not require the user to manually perform setup such as:

```text
create a worktree
choose an implementation directory
register that directory with another system
record a publication destination
mark the contribution as active
mark the contribution as ready
remember which session created it
```

If the eventual architecture requires isolation, durable identity, lifecycle
registration, version-control handoff metadata, or other pre-authoring state,
`proto-go` must cause the required state to exist as part of the managed
implementation lifecycle.

Those mechanisms are product plumbing, not separate user-facing preparation
steps.

Diagnostic, recovery, or administrative interfaces may expose lower-level
mechanisms without making them part of ordinary `proto-go` use.

## 0.4 Concurrent implementation is an ordinary mode

Several `proto-go` implementations may exist concurrently.

The user must not have to serialize otherwise-independent implementation tasks
merely because they modify the same repository or because another implementation
session is currently active.

A contribution being authored by one `proto-go` must not expose its mutable
implementation surface as the ordinary editing surface of another contribution.

The product objective is:

```text
concurrent implementation
→ isolated mutable contributions
→ explicit completion boundaries
```

not:

```text
concurrent implementation
→ shared mutable checkout
→ coordination by convention
```

This requirement concerns authoring isolation.

It does not imply that all resulting contributions can later be integrated
without convergence or semantic conflict.

## 0.5 Implementation completion is an explicit internal lifecycle boundary

The existence of filesystem changes, commits, a clean working tree, elapsed
time, or disappearance of the originating session is not sufficient evidence
that an implementation contribution is complete.

A contribution remains implementation-in-progress until the `proto-go` lifecycle has
established that its required implementation and validation obligations have
been satisfied.

Conceptually:

```text
managed implementation starts
→ IN PROGRESS

required implementation work succeeds
required validation succeeds
→ READY FOR HANDOFF
```

These names describe the product distinction.

They do not prescribe a storage representation, state-machine implementation,
database schema, process model, or public API.

This implementation-completion boundary is significant because downstream
systems may act on ready contributions while they must not treat in-progress
contributions as eligible for publication progression.

It is an internal `proto-go` lifecycle boundary.

It is not the normal successful terminal outcome of `proto-go`.

The exact validation obligations applicable to an implementation occurrence are
not defined by `proto-go` as a universal methodology.

They come from the governing authorities applicable to that implementation
occurrence.

`proto-go` is responsible for identifying those applicable obligations, executing
them or causing them to be executed, and establishing their satisfaction.

If `proto-go` cannot establish the applicable validation-obligation set with
sufficient authority, it must not establish `READY FOR HANDOFF`.

## 0.6 Ready-for-handoff state must survive session loss

Once `proto-go` has established that a contribution is ready for handoff, that
internal lifecycle fact must not depend on the continued existence or memory of:

* the originating chat session;
* the originating main agent;
* the implementation agent;
* the user's recollection of where the contribution was produced.

For example:

```text
proto-go
→ implementation succeeds
→ contribution becomes ready

session closes

later:
downstream version-control progression
→ can still identify the ready contribution
```

Forgetting to invoke the downstream version-control command immediately after
`proto-go` must not make successfully completed implementation work undiscoverable.

## 0.7 Interrupted work remains incomplete

If the implementation lifecycle is interrupted before its completion boundary,
the contribution must remain distinguishable from completed work.

For example:

```text
proto-go
→ implementation begins
→ session disappears before completion
```

must not silently become equivalent to:

```text
proto-go
→ implementation completed successfully
```

A later Development System action may inspect, resume, repair, or abandon the
interrupted contribution.

Downstream publication machinery must not infer semantic completion from the
mere survival of its files, commits, clean status, or process absence.

## 0.8 Ready-for-handoff is internal; publication is the successful terminal outcome

`READY FOR HANDOFF` means that the Development System considers the produced
implementation contribution ready to cross into downstream version-control
progression.

It does not imply that:

- its target has not advanced;
- another contribution does not overlap it;
- it can be immediately fast-forwarded;
- global repository state is already converged;
- no semantic conflict can arise;
- the governing publication outcome has been reached.

Therefore this is a valid intermediate `proto-go` state:

```text
proto-go
→ implementation completed
→ validation completed
→ READY FOR HANDOFF

downstream version-control progression
→ discovers stale or conflicting state
→ cannot progress mechanically
```

That downstream result does not by itself mean that `proto-go` has failed or
completed.

When the current Development System has authority to perform the required
authored correction or convergence work, the normal continuation is:

```text
mechanical publication cannot progress
→ main agent performs required authored work
→ affected implementation work is revalidated as required
→ downstream publication is retried
```

This cycle may repeat until either:

```text
the publication outcome required by governing version-control policy is reached
```

or:

```text
further progress requires unresolved action, decision, permission, or authority
outside the current Development System
```

The normal successful terminal outcome of `proto-go` is the former.

`proto-go` must not report success merely because implementation is complete,
validation passed, `READY FOR HANDOFF` is durable, commits exist, or a downstream
publication attempt occurred.

The exact taxonomy and representation of non-success or blocked outcomes remain
undecided.

The exact route-specific meaning of publication also remains to be derived or
decided for each governing version-control policy.

## 0.9 Semantic authority remains in the Development System

`proto-go` does not determine product meaning independently of the Development
System.

It does not invent:

```text
what feature should be built
whether product scope should change
which unresolved semantic interpretation is correct
whether an ambiguous conflict should prefer one authored meaning
```

Those decisions belong to the user, main agent, governing specification, or
other authorized semantic authority.

Within an authorized implementation task, `proto-go` is responsible for carrying the
user-facing objective through managed implementation, required validation,
durable readiness, and publication.

The Development System retains authority for authored implementation and
semantic correction work.

The downstream version-control system retains authority for the mechanical
version-control progression assigned to it.

The downstream version-control layer must not reconstruct or second-guess the
Development System's implementation-readiness decision from Git state, and
`proto-go` must not reinterpret a downstream mechanical inability to progress as
semantic implementation completion or publication success.

Validation-policy authority also remains outside `proto-go`.

`proto-go` must not replace governing validation requirements with a universal
methodology of its own.

Its responsibility is to identify and satisfy the validation obligations
established by the governing authorities applicable to the implementation
occurrence.

When `proto-go` cannot establish the applicable governing obligation set with
sufficient authority, it must fail closed at the readiness boundary rather than
silently reducing the validation contract.

## 0.10 Product-intent conformance rule

A `proto-go` design is not product-conformant if ordinary safe use requires the user
or main agent to perform avoidable implementation-environment orchestration such
as:

```text
manually create or select an isolation worktree
manually register the contribution before implementation
manually remember which mutable checkout belongs to which proto-go
manually protect concurrent in-progress work from another proto-go
manually record completion somewhere after successful implementation
keep the originating chat session alive so completed work can later be found
reconstruct a completed contribution from filesystem archaeology
allow downstream publication to infer readiness from dirty/clean Git state
manually remember to invoke normal downstream publication after proto-go declares success
treat READY FOR HANDOFF as equivalent to successful proto-go completion
treat a failed mechanical publication attempt as publication success
define a universal built-in validation checklist as the authority for READY FOR HANDOFF
declare READY FOR HANDOFF while the applicable governing validation-obligation set is unknown
treat successful execution of only the validations it happened to discover as proof that no other governing obligation applies
```

Likewise, a design is non-conformant if successful `proto-go` work can become
indistinguishable from unfinished or abandoned work merely because the
originating process or session disappeared.

The reference product-conformance scenario is:

```text
/go A begins implementation.

/go B begins concurrently.

A completes its implementation and validation.
Its contribution reaches READY FOR HANDOFF.

B is still being modified.

A enters downstream version-control progression.

If publication can progress mechanically:
  it progresses.

If authored correction or convergence is required and remains within the
  Development System's authority:
  the main agent performs that work,
  affected work is revalidated,
  and publication is retried.

A reports successful proto-go completion only after the governing publication
outcome is reached.

B remains recognizable as incomplete work and is untouched.

If A's originating session disappears after READY FOR HANDOFF but before
publication, A's ready contribution remains durably distinguishable from B's
incomplete contribution.
```

The architecture used to satisfy this contract may evolve.

The product promise must remain stable.

## 0.11 Invocation and execution model

proto-go is invoked from a main-agent session through the user-facing `/go`
skill invocation.

The `/go` skill governs the main agent's active proto-go progression.

A `/go` invocation enters Invocation Preflight before any logical proto-go
operation is admitted:

```text
/go invocation
        ↓
Invocation Preflight
```

Invocation Preflight is itself artifact-driven.

The main agent derives the best currently available machine-readable invocation
input from the user request, authoritative context, and relevant established
proto-go state.

A fresh terminating proto-go script invocation may then occur before Admission:

```text
Invocation Preflight
        ↓
current machine-readable input
+
sufficient authoritative Progression Context
        ↓
fresh proto-go script invocation
        ↓
mechanical observation / legal progression
        ↓
Continuation Artifact
        ↓
script terminates
        ↓
/go Continuation Policy
        ↓
authorized main-agent continuation
        ↺
```

The continuation may include context resolution, user interaction, authority
acquisition, authored work where semantically permitted, or construction of new
machine-readable invocation input.

If Admission information or authority remains incomplete, Admission is
forbidden, but another pre-Admission script invocation is not thereby
forbidden.

Invocation Preflight may therefore contain repeated terminating script
invocations, Continuation Artifacts, main-agent continuations, and user
clarification.

An admission-complete machine-readable Launch Contract is required before
Admission.

Launch Contract completeness may be established through that pre-Admission
artifact-driven progression.

Admission begins the logical proto-go operation and its
`ManagedContribution`.

After Admission, the first mechanical transition of the admitted logical
proto-go operation is a fresh terminating proto-go script invocation:

```text
admission-complete Launch Contract
        ↓
Admission
        ↓
fresh post-Admission proto-go script invocation
```

A pre-Admission invocation is not resumed across Admission.

Every script invocation terminates before any corresponding main-agent
continuation occurs.

A completed script invocation emits machine-readable Continuation Artifact
information.

The `/go` skill interprets that information under Continuation Policy.

The main agent executes the authorized continuation.

A continuation may later lead to another fresh script invocation:

```text
sufficient authoritative Progression Context
+
current invocation input
        ↓
fresh script invocation
        ↓
Continuation Artifact
        ↓
script terminates
        ↓
sufficient authoritative Progression Context
+
Continuation Artifact
        ↓
/go Continuation Policy
        ↓
authorized main-agent continuation
        ↓
optional fresh script invocation
```

A later invocation is never a resumption of an earlier invocation:

```text
later invocation != resumption of earlier invocation
```

Every transfer of active proto-go control between the main agent and a script
invocation must provide or make resolvable sufficient machine-readable
authoritative Progression Context for the receiving actor to continue correctly.

Correct progression must not depend on implicit conversational memory or on a
previous script process remaining alive.

The artifact-driven progression spans the normal end-to-end proto-go objective.

`READY FOR HANDOFF` is an internal intermediate lifecycle boundary and does not
normally terminate the progression.

The progression may continue through publication, authored correction,
revalidation, additional readiness occurrences, and other authorized
continuations as required.

The normal successful terminal outcome is the governing publication outcome:

```text
PUBLISHED
```

The proto-go script is not the end-to-end workflow orchestrator.

The main agent, governed by the `/go` skill, executes the active procedural
continuations.

This execution model does not define a fixed universal sequence of numbered
stages.

It also does not define the script pathname, language, CLI, invocation-input
format, Continuation Artifact schema, Progression Context representation,
persistence mechanism, correlation mechanism, authoring-isolation mechanism,
workflow engine, or recovery architecture.

# 1. Purpose

Not yet derived.

Do not populate this section until an explicit derivation from the accepted
Product Intent establishes its contents.

# 2. Core mental model

The minimum product-level model currently established is:

```text
Implementation Request
        ↓
Logical proto-go Operation
        ↓
ManagedContribution
        ↓
Managed implementation lifecycle
        ↓
READY FOR HANDOFF
        ↓
Downstream version-control progression
        ↓
governing publication outcome
```

The `ManagedContribution` is the logical implementation contribution owned by
one logical `proto-go` operation.

It is not equivalent to:

```text
repository
branch
worktree
session
agent
process
mutable authoring surface
```

A valid contribution may have managed authoring effects in one repository:

```text
ManagedContribution A
        ↓
repository X
```

or in several repositories:

```text
ManagedContribution B
        ├── repository X
        ├── repository Y
        └── repository Z
```

The existence, name, identity, lifecycle, or representation of any subordinate
repository-local object is not yet defined.

The Development System retains semantic and authored-development authority.

A downstream version-control system retains authority for the mechanical
version-control transitions assigned to it.

`proto-go` composes those responsibilities into its end-to-end
implementation-to-publication objective without collapsing their authority
boundaries.

# 3. Canonical terminology

The following terms are currently canonical.

## Logical `proto-go` Operation

The logical end-to-end product occurrence initiated for one implementation
request.

Its normal successful terminal outcome is the governing publication outcome.

A logical `proto-go` operation is not defined by the lifetime of one chat session,
agent process, operating-system process, or future execution-engine run.

## ManagedContribution

The single logical implementation contribution owned by one logical `proto-go`
operation.

A `ManagedContribution` may contain managed authoring effects in one or more
repositories.

Its identity is not repository identity and is not defined by a branch,
worktree, session, agent, process, or mutable authoring surface.

The concrete representation of `ManagedContribution` identity is not yet
defined.

## READY FOR HANDOFF

The internal lifecycle boundary at which the Development System has
authoritatively established that the current contribution state satisfies the
implementation and validation obligations required before downstream
version-control progression.

`READY FOR HANDOFF` is not successful `proto-go` completion.

Its concrete representation is not yet defined.

## Readiness occurrence

One authoritative establishment of `READY FOR HANDOFF` for a determinate
authored state of a `ManagedContribution`.

A readiness occurrence applies to the repository participation and authored
state evaluated by that readiness decision.

Later authored mutation does not retroactively change what an earlier readiness
occurrence authorized.

The concrete identifier, storage representation, and handoff representation of a
readiness occurrence are not yet defined.

## Governing publication outcome

The publication outcome required by the version-control policy governing the
contribution.

Reaching that outcome is the normal successful terminal condition of the logical
`proto-go` operation.

Route-specific publication semantics are not yet defined by this specification.

## Development System

The authority responsible for product-semantic decisions and authored
development work within the `proto-go` lifecycle.

The concrete harness, process, agent topology, or implementation realizing this
role is not fixed by the Product Intent.

## Governing validation obligation

A validation obligation established by authority governing the current
implementation occurrence and applicable to the contribution state being
evaluated for readiness.

`proto-go` does not make an obligation governing merely by choosing to execute it.

The concrete authority model, representation, discovery mechanism, and evidence
format are not yet defined.

## Applicable validation-obligation set

The set of governing validation obligations applicable to the contribution
state whose readiness is being evaluated.

`READY FOR HANDOFF` may be established only when `proto-go` has sufficient authority
to establish this applicable set and every obligation in it is satisfied.

The exact representation and computation of this set are not yet defined.

## Downstream version-control system

The specialized system responsible for the mechanical version-control
progression assigned to it after the relevant `proto-go` handoff boundary.

Its concrete implementation and API are not yet selected by this specification.

## `/go` skill

The user-facing skill invoked from a main-agent session to initiate a logical
proto-go operation.

The skill supplies the procedure governing the main agent's active proto-go
progression.

The skill is not itself defined as the Product Intent authority; it must execute
the semantics established by the normative proto-go specification.

## proto-go script

The mechanical script whose terminating invocation is the first mechanical
transition after Admission of a logical proto-go operation.

The main agent invokes the script, the script runs to completion, and its outputs
return to the main agent before subsequent proto-go procedure steps continue.

The script is not the end-to-end proto-go workflow orchestrator.

Its pathname, implementation language, CLI, output schema, and internal
architecture are not yet defined.

## Invocation Preflight

The `/go`-governed phase before Admission of a logical proto-go operation.

During Invocation Preflight, the main agent derives the best currently
available machine-readable invocation input from the user request,
authoritative context, and relevant established proto-go state.

Invocation Preflight may contain repeated terminating proto-go script
invocations, Continuation Artifacts, authorized main-agent continuations, and
user clarification.

Invocation Preflight continues until an admission-complete Launch Contract can
be established or the attempted progression does not proceed.

Invocation Preflight itself is not yet the admitted logical proto-go operation.

## Admission

The semantic boundary at which an admission-complete Launch Contract has been
established and the logical proto-go operation is allowed to begin.

Before Admission, an attempted `/go` invocation has not yet admitted its
ManagedContribution.

Admission does not itself imply authoring, readiness, or publication.

## Launch Contract

The machine-readable representation of sufficiently resolved implementation
intent, governing authority basis, and launch premises with which one logical
proto-go operation is admitted.

The Launch Contract may be established through pre-Admission artifact-driven
progression.

The Launch Contract is the initial authority root of the admitted logical
proto-go operation.

It is not defined as equivalent to script arguments, one concrete serialized
document, or one persistence record.

## Continuation Artifact

Machine-readable result information emitted by a completed proto-go script
invocation and consumed under `/go` continuation policy to determine the
applicable next main-agent continuation.

A Continuation Artifact carries mechanical facts or continuation conditions.

It is not itself procedural authority.

## Continuation Policy

The `/go`-owned rules and bounded heuristics that map recognized Continuation
Artifact facts or conditions to authorized main-agent continuation behavior.

Continuation Policy remains subordinate to normative proto-go Product Intent
and applicable governing authority.

## Progression Context

The machine-readable authoritative context sufficient for an actor receiving
proto-go control to continue the relevant progression correctly without
depending on implicit conversational memory.

Progression Context represents the current authoritative progression state and
the relevant provenance, authority, contracts, established facts, and prior
continuation information necessary for correct subsequent progression.

It does not require retention or replay of irrelevant complete history.

Its concrete representation, storage, transport, persistence, reconstruction,
snapshotting, event history, and ownership are not defined.

# 4. Required properties and invariants

The following invariants are normative consequences of the accepted Product
Intent and ADR-001/ADR-002.

No implementation mechanism is implied unless an invariant explicitly requires
one.

## PROTO-GO-INV-001 — One logical operation owns one ManagedContribution

One logical `proto-go` operation MUST own exactly one `ManagedContribution`.

Repository boundaries MUST NOT implicitly split that one logical contribution
into several `ManagedContribution` identities.

## PROTO-GO-INV-002 — ManagedContribution identity is mechanism-independent

A `ManagedContribution` MUST have product-level identity independent of:

- repository identity;
- conversational session identity;
- agent identity;
- process identity;
- branch identity;
- mutable authoring-surface identity.

An implementation MAY bind the contribution to such mechanism-specific objects,
but none of those objects alone may define what the `ManagedContribution` is.

## PROTO-GO-INV-003 — One ManagedContribution may span multiple repositories

A `ManagedContribution` MAY contain managed authoring effects in one or more
repositories while remaining one logical contribution.

The product MUST NOT require the user to split one logical implementation
occurrence into multiple `proto-go` operations solely because repository boundaries
are crossed.

This invariant does not define repository-local substructure or publication
coordination semantics.

## PROTO-GO-INV-004 — Managed identity precedes managed authoring mutation

The `ManagedContribution` identity and the minimum managed lifecycle authority
required to recognize work as belonging to that contribution MUST exist before
the first managed authoring mutation belonging to that contribution occurs.

There MUST NOT be an interval in which `proto-go`-managed authoring mutations have
already begun but the product cannot distinguish those mutations as belonging to
the managed contribution.

This invariant does not select a persistence engine, identifier format, registry,
or worktree mechanism.

## PROTO-GO-INV-005 — Concurrent contributions do not share an ordinary mutable authoring surface

Distinct concurrently authored `ManagedContribution` instances MUST NOT share
the same ordinary mutable authoring surface.

They MAY affect the same repository and MAY logically overlap the same files,
but their ordinary concurrent authoring MUST remain isolated at the mutable
authoring boundary.

This invariant establishes isolation semantics without selecting Git worktrees
or another isolation mechanism.

## PROTO-GO-INV-006 — Readiness is authoritative, not inferred from incidental state

`READY FOR HANDOFF` MUST be established only by authoritative `proto-go` lifecycle
progression.

It MUST NOT be inferred solely from any combination of incidental state such as:

- Git working-tree cleanliness;
- Git dirtiness;
- existence of commits;
- absence of uncommitted changes;
- elapsed time;
- process termination;
- agent termination;
- session disappearance;
- filesystem survival.

Incidental technical state MAY be evidence consumed by governing validation, but
it is not by itself authority to declare readiness.

## PROTO-GO-INV-007 — Readiness is durable before downstream progression

`READY FOR HANDOFF` MUST be durably established before downstream
version-control progression for that readiness occurrence begins.

`proto-go` MUST NOT expose a handoff to downstream progression first and only attempt
to establish the corresponding durable readiness fact afterward.

This ordering preserves the ability to recognize implementation-complete work if
execution or session state disappears during downstream progression.

The persistence mechanism is not defined by this invariant.

## PROTO-GO-INV-008 — Publication is the only normal successful terminal outcome

The normal successful terminal outcome of a logical `proto-go` operation MUST be the
publication outcome required by the governing version-control policy.

None of the following alone constitutes normal successful `proto-go` completion:

```text
implementation complete
validation complete
READY FOR HANDOFF established
local commit exists
downstream publication requested
downstream publication attempted
```

`proto-go` MUST NOT report normal success until the governing publication outcome has
been reached.

## PROTO-GO-INV-009 — Mechanically blocked publication preserves the logical objective when authored progress remains available

When downstream version-control progression cannot progress mechanically, and
the required next work remains within the current Development System's authored
authority, the same logical `proto-go` objective MUST remain in force.

The mechanical block MUST NOT by itself:

* create a new implementation request;
* create a new logical `proto-go` objective;
* redefine the original contribution as successful;
* redefine the original contribution as semantically complete publication.

The Development System MAY perform the required authored correction or
convergence work and retry downstream progression under the same logical `proto-go`
objective.

The concrete retry/process/session representation is not defined here.

## PROTO-GO-INV-010 — Post-readiness authored mutation cannot bypass readiness obligations

If authored work mutates the contribution after a previously established
`READY FOR HANDOFF` boundary, the resulting work MUST satisfy the readiness and
validation obligations applicable to that resulting state before downstream
publication is retried where those obligations require re-establishment.

A previous readiness fact MUST NOT be treated as blanket authorization to publish
later authored mutations without the validation required for those mutations.

This invariant does not define the validation set or incremental-validation
algorithm.

## PROTO-GO-INV-011 — Required contribution lifecycle facts outlive originating execution context

The `ManagedContribution` identity and every lifecycle fact whose survival is
required by the Product Intent MUST NOT depend on the continued existence of:

* the originating conversational session;
* the originating main-agent process;
* an implementation-agent process;
* one operating-system process executing `proto-go`.

Loss of such an execution context MUST NOT make already-established ready work
indistinguishable from incomplete or unrelated work.

This invariant does not define storage or recovery mechanisms.

## PROTO-GO-INV-012 — Product semantics are execution-engine independent — SUPERSEDED

**Status:** Superseded by ADR-006.

This invariant previously asserted that proto-go Product Intent and required
semantics were independent of a particular execution engine.

ADR-006 supersedes that assertion by establishing a specific main-agent,
`/go`-skill, and terminating-script execution model as part of proto-go Product
Intent.

`PROTO-GO-INV-012` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-013 — Validation policy authority remains outside `proto-go`

`proto-go` MUST NOT define a universal hard-coded validation policy as the authority
for `READY FOR HANDOFF`.

A validation method, check, test, review, analysis, or other procedure does not
become a governing readiness obligation merely because `proto-go` knows how to
execute it or considers it desirable.

The governing authority for validation obligations remains external to `proto-go`'s
own methodological preference.

This invariant does not prohibit `proto-go` from using additional internal techniques
or evidence that do not redefine the governing readiness contract.

## PROTO-GO-INV-014 — Readiness requires every applicable governing validation obligation

`READY FOR HANDOFF` MUST NOT be established unless every applicable validation
obligation established from governing authority is satisfied for the
contribution state being evaluated.

Passing only a subset of the applicable governing obligations is insufficient.

Passing a built-in or discovered checklist is insufficient unless the product
has sufficient authority to establish that the checklist covers the applicable
governing obligation set.

This invariant does not define the complete authority graph, obligation
representation, or validation-execution mechanism.

## PROTO-GO-INV-015 — Unknown governing obligation completeness forbids readiness

If `proto-go` cannot establish the applicable validation-obligation set with
sufficient authority, `proto-go` MUST NOT establish `READY FOR HANDOFF`.

Uncertainty about whether additional governing validation obligations apply MUST
fail closed at the readiness boundary.

This invariant does not require `proto-go` to invent additional validation policy.

It requires `proto-go` not to claim readiness when it cannot establish the governing
validation contract applicable to that readiness decision.

## PROTO-GO-INV-016 — Repository scope may expand during authoring

A `ManagedContribution` MAY expand to additional repositories during managed
authoring.

Before the first managed authoring mutation belonging to the contribution occurs
in a newly included repository, that repository MUST already be under the
contribution's managed authoring authority.

Adding a repository under this rule MUST NOT by itself create a new
implementation request, logical `proto-go` operation, or `ManagedContribution`.

This invariant does not define the repository-admission mechanism or persistence
representation.

## PROTO-GO-INV-017 — Each readiness occurrence binds a determinate authored state

Each `READY FOR HANDOFF` occurrence MUST apply to a determinate authored state
of the `ManagedContribution`.

The readiness occurrence MUST bind the repository participation relevant to that
authored state.

Readiness MUST NOT be interpreted as an unversioned permanent boolean that
automatically authorizes arbitrary future states of the same contribution.

This invariant does not define a concrete snapshot representation.

## PROTO-GO-INV-018 — Later authored mutation requires new applicable readiness

Authored mutation after a `READY FOR HANDOFF` occurrence produces authored work
that is not authorized for downstream progression merely by that earlier
readiness occurrence.

Before downstream progression of the resulting authored state, `proto-go` MUST
establish a new applicable readiness occurrence after satisfying the governing
readiness and validation obligations for that resulting state.

This applies whether the later authored mutation changes an already
participating repository or adds a newly participating repository.

## PROTO-GO-INV-019 — Downstream progression operates on the bound ready state

Downstream progression for one `READY FOR HANDOFF` occurrence MUST operate on
the authored state bound by that readiness occurrence.

The Development System MUST NOT continue mutating that authored state underneath
the downstream progression while treating the same readiness occurrence as
authority for the moving target.

This invariant does not prohibit downstream mechanical version-control
realization of the bound authored state.

## PROTO-GO-INV-020 — One readiness occurrence forms one logical publication unit

All managed effects bound by one `READY FOR HANDOFF` occurrence MUST form one
indivisible logical publication unit for governing publication semantics.

The publication unit follows the readiness occurrence rather than independent
repository-local `proto-go` completion.

This invariant does not introduce a separate canonical product object.

## PROTO-GO-INV-021 — No proper subset may independently reach governing publication

No proper subset of the managed effects belonging to one logical publication
unit MAY independently reach its governing publication outcome while the
remainder of that unit has not.

Progressive preparation MAY occur when those preparatory effects do not
independently constitute governing publication outcomes.

This invariant applies to governing publication semantics, not to every
mechanical intermediate operation.

## PROTO-GO-INV-022 — Incapable routes must not begin irreversible partial publication

`proto-go` MUST NOT begin a publication transition that can irreversibly expose a
partial governing publication outcome when the selected downstream route cannot
preserve the all-or-none governing publication semantics required for the
logical publication unit.

Inability of a route to preserve that property MUST NOT silently degrade the
publication contract to best-effort repository-local publication.

This invariant does not select the mechanism by which a conforming route
provides the required publication semantics.

## PROTO-GO-INV-023 — Main-agent skill orchestration

A proto-go operation MUST be initiated through the user-facing `/go` invocation
in a main-agent session.

The `/go` skill MUST supply the procedure governing that main agent's active
proto-go progression.

The main agent executes the procedure subject to the normative proto-go Product
Intent; it does not acquire authority to invent missing product semantics.

## PROTO-GO-INV-024 — First procedural step is a terminating proto-go-script invocation — SUPERSEDED

**Status:** Superseded by ADR-008.

This invariant previously asserted that invocation of the proto-go script was
the first procedural step after `/go` invocation.

ADR-008 supersedes that ordering by establishing Invocation Preflight and
Admission before the first mechanical proto-go script transition.

The terminating-script requirement itself remains part of the current Product
Intent and is represented by the later normative invariants introduced by
ADR-008 together with `PROTO-GO-INV-025`.

`PROTO-GO-INV-024` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-025 — Active script execution cannot contain a suspended main-agent continuation

An active proto-go script invocation MUST NOT require suspension into a
main-agent authored continuation followed by resumption of that same script
execution.

The proto-go script MUST NOT own end-to-end proto-go orchestration.

Main-agent authored work required by proto-go MUST occur while the main agent
holds procedural control under the `/go` skill, outside an active proto-go
script invocation.

## PROTO-GO-INV-026 — Authored resumption retires prior readiness publication authority

Before managed authored mutation resumes after a `READY FOR HANDOFF` occurrence,
that readiness occurrence MUST cease to authorize future crossing of the
governing publication boundary for its bound authored state.

Retiring that publication authority MUST occur before the first subsequent
managed authored mutation.

Retirement of publication authority MUST NOT retroactively invalidate the
historical fact that the readiness occurrence was validly established.

A retired readiness occurrence MUST NOT later be reactivated as publication
authority. A later eligible authored state requires a new applicable readiness
occurrence.

This invariant does not define how publication authority or its retirement is
represented.

## PROTO-GO-INV-027 — Retired readiness cannot later publish

Once the publication authority of a readiness occurrence has been retired,
downstream progression originating from that readiness occurrence MUST NOT
subsequently cause its bound logical publication unit to reach the governing
publication outcome.

Preparatory effects created before retirement MAY remain.

Such preparatory effects MUST NOT retain effective authority capable of
publishing the retired readiness occurrence.

This invariant does not require destruction, rollback, or garbage collection of
preparatory effects.

## PROTO-GO-INV-028 — Publication authority is fenced before authored resumption

Managed authored mutation MUST NOT resume after a publication-authorizing
readiness occurrence until proto-go has established that progression authorized
by that readiness occurrence can no longer cross the governing publication
boundary.

A local lifecycle-state change is insufficient when already-originated
downstream progression could still later cross that boundary.

The fencing mechanism is not defined by this invariant.

No lock, lease, generation, token, cancellation mechanism, compare-and-swap
operation, Git-ref protocol, persistence engine, or downstream protocol is
selected here.

## PROTO-GO-INV-029 — Invocation preflight precedes operation admission

A `/go` invocation MUST enter main-agent Invocation Preflight before a logical
proto-go operation is admitted.

Invocation Preflight MUST resolve the intent, governing authority basis, and
launch premises required for Admission.

Invocation Preflight MAY perform multiple main-agent/user clarification turns
before Admission.

An incomplete `/go` invocation MUST NOT by itself cause a logical proto-go
operation or `ManagedContribution` to be admitted.

This invariant does not define preflight persistence or recovery.

## PROTO-GO-INV-030 — Admission requires a machine-readable Launch Contract — SUPERSEDED

**Status:** Superseded by ADR-009.

This invariant previously required the main agent to establish an
admission-complete machine-readable Launch Contract before the logical proto-go
operation was admitted.

The requirement that Admission requires an admission-complete Launch Contract
remains part of current Product Intent.

ADR-009 supersedes the previous allocation and ordering by allowing Launch
Contract completeness to be established through pre-Admission artifact-driven
progression involving terminating proto-go script invocations and authorized
main-agent continuations.

The corrected requirement is represented by `PROTO-GO-INV-039`.

`PROTO-GO-INV-030` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-031 — The Launch Contract is the initial authority root

The admitted Launch Contract MUST form the initial authority root of the logical
proto-go operation.

Normative contracts, obligations, or semantic decisions used by proto-go during
the operation MUST derive from:

- authority represented by the admitted Launch Contract;
- authoritative facts resolved under that authority; or
- explicit additional authority obtained through an authorized continuation.

The main agent, proto-go script, and downstream mechanical systems MUST NOT
invent missing semantic authority merely because progression requires it.

This invariant does not define how later authority additions or amendments are
represented.

## PROTO-GO-INV-032 — Missing admission authority fails closed — SUPERSEDED

**Status:** Superseded by ADR-009.

This invariant previously required missing information or authority needed for
Admission both to prevent Admission and to prevent invocation of the proto-go
script.

ADR-009 preserves the fail-closed Admission boundary but supersedes the
prohibition on pre-Admission script execution.

Missing Admission information or authority still prevents Admission.

It does not by itself prevent a terminating proto-go script invocation during
Invocation Preflight.

The corrected semantics are represented by `PROTO-GO-INV-038` and
`PROTO-GO-INV-039`.

`PROTO-GO-INV-032` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-033 — First mechanical transition after admission is a fresh terminating script invocation

After Admission of a logical proto-go operation, its first mechanical transition
MUST be a fresh invocation of the proto-go script.

That invocation MUST consume machine-readable invocation input derived from the
admitted Launch Contract and applicable authoritative operation state.

The invocation MUST terminate before any main-agent continuation based on its
results occurs.

This invariant does not define the script pathname, language, CLI, invocation
input schema, transport, or internal implementation.

## PROTO-GO-INV-034 — Completed script invocation emits machine-readable continuation artifacts

Each completed proto-go script invocation MUST emit machine-readable result
artifact information sufficient for the `/go` procedure to classify the
mechanical result and determine the applicable continuation rule.

Such result artifact information is canonicalized as one or more Continuation
Artifacts.

This invariant does not define their number, serialization, schema, enum values,
storage, filenames, or transport.

## PROTO-GO-INV-035 — Continuation artifacts do not hold procedural authority

A Continuation Artifact MUST NOT itself acquire authority to command arbitrary
main-agent behavior.

The `/go` skill MUST define the Continuation Policy that interprets recognized
Continuation Artifact facts or conditions and determines the permitted or
required main-agent continuation.

The main agent MUST execute continuation under that `/go` policy and applicable
governing authority rather than treating script output as independent
procedural authority.

Continuation Policy MAY contain predefined heuristics, but those heuristics
MUST NOT authorize invention of missing product semantics or missing authority.

## PROTO-GO-INV-036 — Main-agent continuation occurs only after script termination

Any main-agent continuation caused by the results of a proto-go script
invocation MUST occur only after that invocation has terminated.

Such continuation MAY include authored work, context resolution, user
interaction, authority acquisition, or construction of machine-readable input
for later mechanical progression when permitted by the governing `/go`
Continuation Policy.

No such continuation MAY be modeled as suspension into the main agent followed
by resumption of the same script invocation.

This invariant preserves and generalizes the terminating boundary already
required by `PROTO-GO-INV-025`.

## PROTO-GO-INV-037 — Mechanical re-entry uses a fresh script invocation

After an authorized main-agent continuation, proto-go MAY perform another
mechanical transition by invoking the proto-go script again with newly derived
machine-readable invocation input.

Every such invocation MUST be a fresh invocation.

A later invocation MUST NOT be treated as resumption of an earlier terminated
invocation.

A single logical proto-go operation MAY therefore contain multiple terminating
proto-go script invocations separated by authorized main-agent continuations.

This invariant does not define workflow persistence, process supervision, or
recovery mechanics.

## PROTO-GO-INV-038 — Pre-admission progression may invoke the proto-go script

During Invocation Preflight, the main agent MUST derive the best currently
available machine-readable invocation input from the user request, sufficiently
authoritative available context, and relevant already-established proto-go
state.

Invocation Preflight MAY invoke the proto-go script before Admission and before
an admission-complete Launch Contract exists.

Each such pre-Admission script invocation MUST remain terminating and MUST
return Continuation Artifact information before any resulting main-agent
continuation occurs.

Missing Admission information or authority MUST prevent Admission, but MUST NOT
by itself prohibit a pre-Admission proto-go script invocation.

This invariant does not define the invocation-input schema, correlation
mechanism, persistence mechanism, or pre-Admission state representation.

## PROTO-GO-INV-039 — Admission requires a Launch Contract established through authoritative progression

A logical proto-go operation MUST NOT be admitted until an admission-complete
machine-readable Launch Contract has been established.

The Launch Contract MAY be established through repeated pre-Admission
progression consisting of terminating proto-go script invocations,
Continuation Artifacts, authorized main-agent continuations, authoritative
context resolution, and user clarification where required.

The Launch Contract MUST represent sufficiently resolved implementation intent,
governing authority basis, and launch premises for Admission.

Neither the main agent nor the proto-go script MAY invent missing semantic
authority in order to make the Launch Contract complete.

This invariant does not define which component stores the Launch Contract, its
schema, serialization, persistence representation, or versioning model.

## PROTO-GO-INV-040 — `/go` re-entry continues an outstanding authorized progression

When `/go` is invoked in response to an outstanding authorized proto-go
continuation, that invocation MUST continue the relevant existing progression
rather than create a distinct logical proto-go objective merely because `/go`
was invoked again.

Before Admission, such re-entry continues the relevant pre-Admission
progression without retroactively creating a ManagedContribution.

After Admission, such re-entry MUST preserve the same logical proto-go operation
and ManagedContribution.

Every resulting proto-go script execution remains a fresh terminating
invocation.

This invariant does not define how the relevant progression is identified,
correlated, rediscovered, persisted, transported across sessions, or selected
when multiple progressions exist.

## PROTO-GO-INV-041 — Artifact-driven progression spans the objective through publication

The `/go` artifact-driven continuation progression MUST remain capable of
carrying the same logical proto-go objective through the lifecycle required to
reach its governing publication outcome.

`READY FOR HANDOFF` MUST NOT be treated as the normal successful terminal
condition of that progression.

Where continued progression requires additional mechanical execution,
main-agent authored work, validation, user-authority resolution, publication
progression, fencing, correction, or another authorized continuation, the same
proto-go objective continues through fresh terminating script invocations and
authorized main-agent continuations as applicable.

The normal successful terminal condition of the artifact-driven proto-go
progression MUST be the governing publication outcome required by
`PROTO-GO-INV-008`.

This invariant does not define a fixed universal sequence of intermediate
stages.

## PROTO-GO-INV-042 — Control transfer requires sufficient authoritative Progression Context

Every transfer of active proto-go control between the main agent and a
proto-go script invocation MUST provide or make resolvable sufficient
machine-readable authoritative Progression Context for the receiving actor to
continue the relevant progression correctly.

For a proto-go script invocation, the available Progression Context together
with current machine-readable invocation input MUST be sufficient to establish
the relevant current progression state and determine the mechanical transitions
currently permitted.

For a main-agent continuation, the available Progression Context together with
the resulting Continuation Artifact MUST be sufficient to establish the
relevant prior state, the mechanical progression just performed, the
authoritative facts established, and the continuation condition now applicable.

Correct proto-go progression MUST NOT depend on implicit conversational memory
of the main agent, the originating session, or a prior script process.

This requirement does not require retention or replay of irrelevant complete
history.

This invariant does not select a snapshot representation, event log, artifact
chain, database, persistence mechanism, workflow engine, context reconstruction
algorithm, or transport mechanism.

# 5. Lifecycle semantics

The artifact-driven proto-go progression begins before Admission and continues
through the governing publication outcome:

```text
/go
        ↓
pre-Admission artifact-driven progression
        ↺
admission-complete Launch Contract
        ↓
Admission
        ↓
ManagedContribution
        ↓
artifact-driven progression
        ↺
READY
        ↓
artifact-driven progression continues
        ↺
possible correction / new READY
        ↺
PUBLISHED
        ↓
normal success
```

`READY FOR HANDOFF` is not normal termination of the artifact-driven
progression.

Each script invocation remains terminating, and each main-agent continuation
occurs only after the corresponding script invocation has terminated.

The currently established lifecycle ordering is limited to:

```text
managed contribution identity established
        ↓
managed authoring
        ↓
required implementation / validation obligations satisfied
        ↓
READY FOR HANDOFF durably established
        ↓
downstream version-control progression
        ↓
governing publication outcome
```

`READY FOR HANDOFF` is an internal durable lifecycle boundary.

The governing publication outcome is the normal successful terminal outcome.

If downstream progression becomes mechanically blocked while authored progress
remains within Development System authority, the logical `proto-go` objective
continues through authored correction/convergence, applicable revalidation, and
publication retry.

The exact lifecycle-state machine, state names beyond currently canonical terms,
persistence representation, retry representation, blocking taxonomy, recovery
API, and process topology remain undecided.

A `ManagedContribution` may expand its repository participation while managed
authoring is active.

For each newly included repository:

```text
repository required
        ↓
repository enters ManagedContribution managed authoring authority
        ↓
first managed authoring mutation may occur
```

A readiness occurrence then applies to the resulting determinate authored state:

```text
authoring state S1
        ↓
applicable validation/readiness obligations satisfied
        ↓
READY #1 binds S1
```

If authored work resumes:

```text
READY #1
        ↓
authored mutation
        ↓
state S2
        ↓
READY #1 does not authorize S2
        ↓
new applicable readiness required
```

Before that authored mutation may resume, the publication authority of the
prior readiness occurrence must first be retired and fenced:

```text
READY #1
        ↓
downstream progression cannot continue mechanically
        ↓
authored correction required
        ↓
retire publication authority of READY #1
        ↓
establish READY #1 can no longer cross governing publication boundary
        ↓
authored mutation may resume
        ↓
state S2
        ↓
new applicable readiness required
```

The historical fact that `READY #1` was validly established remains true.

What changes is its authority to reach publication.

At any time, no more than one readiness occurrence of the same
`ManagedContribution` may retain effective authority to reach the governing
publication outcome.

This requirement does not define a concrete lifecycle-state representation or
fencing mechanism.

The logical `ManagedContribution` itself remains the same unless some separate
future semantic rule says otherwise.

# 6. Isolation semantics

Concurrent `ManagedContribution` instances require isolated ordinary mutable
authoring surfaces.

This specification currently requires the isolation property expressed by
`PROTO-GO-INV-005`.

It does not yet select the mechanism that provides that property.

In particular, this section does not yet require:

```text
Git worktrees
branches
copies
containers
virtual filesystems
sandboxes
```

or any other concrete isolation mechanism.

Dynamic repository admission does not weaken the isolation requirement.

A newly participating repository must enter the contribution's managed
authoring authority before its first managed authoring mutation.

The concrete mechanism establishing that authority and isolation remains
undecided.

# 7. Completion and validation semantics

`proto-go` owns the operational progression required to establish implementation
readiness.

It does not own validation-policy authority.

The currently established validation flow is:

```text
implementation state to evaluate
        ↓
establish governing validation authority sufficiently
        ↓
establish applicable validation-obligation set
        ↓
execute / cause execution of applicable obligations
        ↓
establish every applicable obligation satisfied
        ↓
READY FOR HANDOFF may be established
```

The following flow is forbidden:

```text
run whatever checks proto-go happens to know
        ↓
those checks pass
        ↓
assume validation is complete
        ↓
READY FOR HANDOFF
```

when `proto-go` lacks sufficient authority to establish that the executed checks
cover the applicable governing obligation set.

Unknown completeness fails closed:

```text
applicable validation-obligation set cannot be established
        ↓
READY FOR HANDOFF forbidden
```

This section does not yet define:

```text
validation-authority precedence
authority conflict resolution
obligation encoding
obligation discovery
evidence format
validation caching
evidence reuse
invalidation rules
incremental validation
validation scheduling
validation parallelism
```

Those remain future derivation or decision work.

# 8. Handoff semantics

The currently established handoff contract is limited to the following:

1. the Development System authoritatively establishes implementation readiness;
2. `READY FOR HANDOFF` becomes durable;
3. downstream version-control progression may then begin;
4. downstream mechanics do not acquire authority to infer semantic readiness;
5. downstream mechanical blockage may return required authored work to the
   Development System while preserving the same logical `proto-go` objective;
6. `proto-go` reaches normal successful completion only when the governing
   publication outcome has been reached.

The exact handoff payload, API, identifier mapping, transport, process boundary,
repository-local decomposition, downstream implementation, and publication-route
semantics remain undecided.

Each downstream handoff is associated with a specific readiness occurrence.

The downstream system must progress the authored state bound by that readiness
occurrence rather than an authored target that continues changing underneath the
handoff.

The managed effects bound by that readiness occurrence form one logical
publication unit.

Downstream progression may prepare parts of that unit independently where
permitted, but no proper subset may independently cross its governing
publication boundary while the remainder has not.

A route unable to preserve the required all-or-none governing publication
semantics must not be used to begin an irreversible partial governing
publication.

If downstream progression for a readiness occurrence requires authored
correction or convergence, proto-go must retire and fence that readiness
occurrence's publication authority before authored mutation resumes.

After fencing:

```text
historical readiness
→ remains true

future publication authority from that readiness
→ absent
```

Already-created downstream preparation may remain, but it must not retain
effective authority to cross the governing publication boundary for the retired
readiness occurrence.

A later authored state that becomes eligible for downstream progression requires
a new applicable readiness occurrence.

# 9. Failure, interruption, and recovery semantics

Not yet derived.

# 10. Concurrency semantics

Multiple logical `proto-go` operations may exist concurrently.

Distinct concurrently authored `ManagedContribution` instances must preserve
the mutable-authoring isolation required by `PROTO-GO-INV-005`.

Concurrency may occur even when contributions:

```text
touch the same repository
touch several repositories
overlap the same logical files
```

This specification does not yet define scheduling, fairness, locking,
serialization, reconciliation ordering, or publication coordination.

Those mechanisms must preserve the accepted Product Intent and invariants.

Concurrency between different `ManagedContribution` instances is distinct from
the stability required inside one active readiness handoff.

For one readiness occurrence, downstream progression must observe the determinate
authored state bound by that occurrence.

This does not impose a repository-wide prohibition on unrelated concurrent work.

# 11. Harness-integration boundary

proto-go is invoked within a main-agent session through the user-facing `/go`
skill.

The `/go` skill supplies the procedure governing the main agent's active
proto-go progression.

The first mechanical transition after Admission invokes the proto-go script as a
terminating call.

After that call returns, the main agent continues the procedure using the state
and outputs produced by the script.

This product-level execution boundary does not make all surrounding harness
policy part of proto-go.

In particular, the surrounding harness's permission-enforcement policy about
whether implementation is allowed outside proto-go remains external to
proto-go Product Intent.

The concrete skill-discovery, installation, harness, process, and recovery
mechanisms remain undecided.

# 12. Version-control-system boundary

Not yet derived.

The Product Intent requires a downstream version-control handoff boundary but
does not yet select or normatively bind a concrete downstream implementation.

# 13. Architectural implications

Not yet derived.

Implementation pressure must not silently fill these sections.
