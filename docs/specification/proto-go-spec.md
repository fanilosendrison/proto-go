# proto-go Specification

> Working product specification derived from the current product discussion.
>
> This document intentionally starts from product intent before fixing
> implementation mechanisms. Managed Git authoring isolation is product-mandated:
> proto-go provisions dedicated temporary detached Git worktrees. Their
> filesystem path, naming, registry representation, cleanup implementation,
> base-commit selection algorithm, and reconstruction mechanism remain
> unspecified. Terms such as persistence engine, database, process model, agent
> topology, harness API, version-control adapter, programming language, and
> runtime remain deliberately unspecified unless the product contract later
> requires them.

# 0. Product intent — governing user experience

This section is normative for the current product direction.

It states the product outcome that lower-level design exists to serve.

ADR-001 established publication as the normal successful terminal outcome of
`proto-go` and made `READY FOR HANDOFF` an internal durable lifecycle boundary.
ADR-013 later superseded ADR-001's terminality assertion while preserving that
publication is mandatory for normal successful completion.

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

ADR-005 established that the managed effects bound by one `READY FOR HANDOFF`
occurrence form one indivisible logical publication unit with all-or-none
governing publication semantics. ADR-014 supersedes that all-or-none
cross-repository publication-visibility semantics in favor of aggregate
publication completion.

ADR-006 originally established main-agent skill orchestration and terminating
proto-go script execution as proto-go's execution model. ADR-017 supersedes
that execution model. `/go` remains the user-facing invocation, and the
proto-go workflow owns its domain progression while Prelock provides
generic execution continuity. ADR-006's supersession of the
execution-engine-independence assertion previously recorded as
`PROTO-GO-INV-012` is unaffected; that identity remains superseded.

ADR-007 establishes that a readiness occurrence must lose effective publication
authority and be fenced from crossing the governing publication boundary before
managed authored mutation resumes. Historical readiness remains valid, but a
retired readiness occurrence cannot later publish. ADR-015 narrows that fencing
to the ManagedContribution-level publication boundary and to new publication
initiation; already-originated repository-local publication may complete after
retirement.

ADR-008 established Invocation Preflight, Admission, Launch Contract authority,
and fail-closed Admission. ADR-017 preserves those business boundaries and
supersedes the terminating-script invocation, Continuation Artifact,
Continuation Policy, and artifact-driven main-agent continuation mechanics.
ADR-008 superseded `PROTO-GO-INV-024`; ADR-009 later corrected its
pre-Admission ordering. Those identities remain superseded.

ADR-009 corrected ADR-008's pre-Admission ordering by allowing Invocation
Preflight to progress before an admission-complete Launch Contract exists. It
established state/authority-driven progression, re-entry continuing the same
logical objective, `READY FOR HANDOFF` as intermediate, and progression through
the governing publication outcome. ADR-017 preserves those semantics and
supersedes Progression Context and the script/artifact runtime mechanics.
ADR-009 superseded `PROTO-GO-INV-030` and `PROTO-GO-INV-032`; those identities
remain superseded.

ADR-010 requires proto-go-managed authored mutation to occur in dedicated
temporary detached Git worktrees bound to one `ManagedContribution` and
participating repository, provisioned automatically before the first managed
authored mutation in that repository.

ADR-011 permitted distinct proto-go progressions to advance concurrently and
established that a progression is not owned by the conversational session that
initiated it. ADR-017 preserves the user-visible concurrency and
business-identity consequences; generic session-transfer and controller
coordination are provided by Prelock.

ADR-012 required `/go` procedural instructions to be loadable incrementally
from a bounded bootstrap. ADR-017 supersedes that requirement as proto-go
Product Intent; instruction loading and execution continuity belong to
Prelock and the surrounding harness.

ADR-013 generalized continuation to actionable mechanical problems and
proto-go-owned closure obligations, and established that `PUBLISHED` is
authoritative historical truth and that normal successful completion requires
publication plus satisfaction of all applicable proto-go-owned closure
obligations. ADR-017 preserves those semantics and supersedes the
artifact/script/main-agent continuation mechanism. ADR-013's supersession of
`PROTO-GO-INV-008` and `PROTO-GO-INV-041` remains historical.

ADR-014 makes multi-repository publication an aggregate completion condition:
one readiness occurrence defines a complete set of independently satisfiable
Repository Publication Obligations, and the `ManagedContribution` may establish
`PUBLISHED` only when an effective publication-authorizing readiness occurrence
exists and every obligation established for it is authoritatively satisfied.
Cross-repository atomic publication visibility is not required.

ADR-015 allows repository-local publication operations already originated
before readiness retirement to complete afterward without being cancelled,
fenced, or awaited as a precondition to authored resumption. Retirement
prevents the retired readiness occurrence from establishing the
`ManagedContribution`'s `PUBLISHED` fact and from initiating new publication
work.

ADR-016 establishes that `proto-go`'s implementation-to-publication procedure
must support local procedural evolution and remain explicitly identifiable as a
composition of independently understandable procedural responsibilities, so
that a procedural change whose semantic effect is local does not force
unrelated changes across the rest of the procedure. ADR-017 places that
requirement in the new execution boundary and adds that proto-go's business
workflow must not emerge implicitly from generic runtime plumbing. It does not
select a concrete composition representation.

ADR-017 establishes that proto-go is the implementation-to-publication workflow
that owns domain semantics and progression decisions, while Prelock
provides generic execution continuity and control-transfer mechanics. proto-go
no longer owns generic workflow-execution machinery. proto-ruu is the
specialized routine-Git-versioning workflow used by proto-go through an
explicit ownership boundary.

The current repository intentionally does not yet derive the complete invariant
set or architecture from this Product Intent. Those derivations must occur
explicitly rather than being invented during implementation.

`proto-go` operates inside a coding-agent development system. Policies controlling
whether implementation is permitted to occur outside `proto-go` belong to the
surrounding harness and are not part of `proto-go` product semantics.

## 0.1 Product definition: managed implementation-to-publication workflow

`proto-go` is the **end-to-end implementation-to-publication procedure of the
coding-agent Development System**.

Its execution is provided by Prelock; the proto-go workflow owns its
domain semantics and progression decisions.

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

Managed authored mutation is performed only in proto-go-managed authoring
worktrees.

For every participating repository in which the admitted contribution requires
managed authored mutation, proto-go automatically establishes a dedicated
temporary detached Git worktree before the first such mutation.

The invoking checkout, the user's ordinary checkout, and another contribution's
managed authoring worktree are not managed authoring surfaces.

It exists so that, once the user and Development System have determined what
should be implemented, that implementation can be carried through managed
authoring, validation, durable readiness, and publication without requiring the
user to manually provision or coordinate the mutable development environment or
manually bridge the normal implementation-to-publication lifecycle.

A `proto-go` operation owns the user-facing implementation objective from the
beginning of managed authoring until the contribution either:

- reaches the publication outcome required by the governing version-control
  policy and satisfies all applicable proto-go-owned closure obligations; or
- cannot satisfy those requirements because further progress requires
  unresolved action, decision, permission, or authority outside the current
  Development System.

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
applicable proto-go-owned closure obligations
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
  PUBLISHED is established.

When all applicable proto-go-owned closure obligations are also satisfied:
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

`proto-go` automatically establishes the managed authoring worktree required for
each participating repository, together with durable identity, lifecycle
registration, version-control handoff metadata, and any other pre-authoring
state required as part of the managed implementation lifecycle.

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

Distinct proto-go progressions may advance concurrently. `proto-go` must not
require global serialization of otherwise-independent `/go` progressions.
Generic session coordination and control ownership for those progressions are
provided by Prelock and are not proto-go Product Intent.

Distinct progressions must not share a managed authoring worktree.

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

A proto-go progression's business identity is not owned by the conversational
session that started or previously advanced it. The lifecycle facts, readiness
state, and managed authoring bindings required for continued proto-go
progression must survive loss or replacement of any execution context. The
generic mechanism by which a later execution context discovers and continues
an active workflow execution is provided by Prelock and is not selected
here.

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

Continued progression of the same contribution by a later execution context
is permitted. It does not by itself change the lifecycle state of the
contribution; only authoritative proto-go workflow progression can establish
readiness, retirement, or publication.

Downstream publication machinery must not infer semantic completion from the
mere survival of its files, commits, clean status, or process absence.

## 0.8 Ready-for-handoff is internal; publication is required for normal successful completion

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

The former is required for normal successful completion.

An established `PUBLISHED` fact may coexist with remaining proto-go-owned
closure obligations. Normal successful completion additionally requires every
applicable proto-go-owned closure obligation to be satisfied.

For a `ManagedContribution` spanning several repositories, repository-local
publication outcomes may occur independently and at different times. The
`ManagedContribution` establishes `PUBLISHED` only when an effective
publication-authorizing readiness occurrence exists and every Repository
Publication Obligation established for it is authoritatively satisfied. No
cross-repository atomic publication visibility is required.

After the governing publication outcome is established, proto-go performs or
attempts its remaining proto-go-owned closure obligations, including normal
automatic retirement and removal of temporary managed worktrees. A later local
cleanup failure does not revert `PUBLISHED`; it is surfaced as an actionable
proto-go condition so repair can be attempted within the same proto-go
objective.

`proto-go` must not report success merely because implementation is complete,
validation passed, `READY FOR HANDOFF` is durable, commits exist, or a downstream
publication attempt occurred.

Nor is an established publication outcome sufficient while applicable
proto-go-owned closure obligations remain unsatisfied.

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
managed authoring mutates the invoking or current ordinary checkout
managed authoring requires the user to create or select a worktree
session replacement creates a second worktree for the same contribution and repository
different progressions are globally serialized without semantic need
actionable proto-go conditions terminate without allowing the same proto-go objective to continue when authorized progress remains possible
PUBLISHED cleanup failure is silently ignored
PUBLISHED is made false because local cleanup failed
a proper subset of repository publication obligations is treated as ManagedContribution PUBLISHED
repository A publication is treated as partial proto-go success
an implementation requires cross-repository atomic publication visibility even though no governing policy separately requires it
a retired readiness initiates new repository-local publication work
a retired readiness becomes PUBLISHED merely because all of its already-originated repository operations later finish
repository-local publication facts are erased merely because their originating readiness was retired
a later readiness blindly inherits prior obligation satisfaction without checking its own exact authored-state and publication requirement
```

The following are conformant and must not be classified as non-conformant:

```text
repository A is published while B remains unpublished
an already-originated repository publication completes after readiness retirement
a later readiness finds an identical publication obligation already satisfied by authoritative current reality
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
  the requested authored work is performed,
  affected work is revalidated,
  and publication is retried.

A reports successful proto-go completion only after the governing publication
outcome is reached and all applicable proto-go-owned closure obligations are
satisfied.

B remains recognizable as incomplete work and is untouched.

If A's originating session disappears after READY FOR HANDOFF but before
publication, A's ready contribution remains durably distinguishable from B's
incomplete contribution.
```

The architecture used to satisfy this contract may evolve.

The product promise must remain stable.

## 0.11 Invocation, workflow, and execution boundary

A `/go` invocation occurs in a main-agent session and is the user-facing entry
point of a logical proto-go operation. It begins a proto-go workflow execution
or continues an applicable existing proto-go workflow execution.

proto-go is the implementation-to-publication workflow. It owns:

```text
what its current business state means
what authority is available
which business obligations remain
which progression is semantically legal
which execution it requests next
how returned execution truth affects proto-go business state
when proto-go is semantically complete
```

Generic execution continuity and control-transfer mechanics are provided by
Prelock. Prelock owns the reusable execution substrate: mechanical
execution, main-agent continuation, execution occurrence truth, and
child-workflow execution with structured return. proto-go does not define
Prelock's generic execution semantics.

Conceptually:

```text
user invokes /go
        ↓
proto-go workflow execution begins or applicable existing execution is continued
        ↓
proto-go evaluates its own authoritative workflow/domain state
        ↓
proto-go determines the semantically legal next progression
        ↓
proto-go requests an execution form
        ↓
Prelock realizes the requested execution
        ↓
execution truth / child result becomes available
        ↓
proto-go interprets it according to proto-go semantics
        ↺
```

The proto-go workflow may currently request execution such as:

```text
mechanical execution
main-agent continuation
child workflow call to proto-ruu
```

but proto-go does not define Prelock's generic semantics for those
execution forms.

A `/go` invocation may enter Invocation Preflight before a logical proto-go
operation is admitted:

```text
/go invocation
        ↓
Invocation Preflight
```

Invocation Preflight is proto-go business and authority resolution before
Admission. The main agent derives the best currently available machine-readable
invocation input from the user request, authoritative context, and relevant
established proto-go state.

Pre-Admission progression may itself require execution realized by
Prelock, including mechanical execution, main-agent work, user
interaction, or child-workflow calls, before an admission-complete Launch
Contract exists.

If Admission information or authority remains incomplete, Admission is
forbidden; pre-Admission progression is not thereby forbidden.

An admission-complete machine-readable Launch Contract is required before
Admission. Admission begins the logical proto-go operation and its
`ManagedContribution`. The Launch Contract remains proto-go's business and
authority contract; its schema, serialization, and storage are not defined
here.

After Admission, the proto-go workflow owns continued business progression.
`READY FOR HANDOFF` is an internal intermediate lifecycle boundary and does not
normally terminate the progression. The progression may continue through
publication, authored correction, revalidation, additional readiness
occurrences, and other authorized continuations as required.

Normal successful completion requires the governing publication outcome and
satisfaction of all applicable proto-go-owned closure obligations. A later
local cleanup failure does not revert `PUBLISHED`; it is surfaced as an
actionable proto-go condition within the same objective.

The main agent may perform authored work when the proto-go workflow requests
it. The main agent is not the global workflow orchestrator merely because it
performs an agentic continuation.

Distinct proto-go progressions may advance concurrently. Proto-go does not
require global serialization of otherwise-independent `/go` progressions.
Generic control ownership, session-transfer, and execution-continuity
coordination are provided by Prelock.

Transient execution or session loss must not change proto-go business truth
such as contribution identity, readiness, or managed authoring bindings.
Generic execution re-entry and correlation are provided by Prelock and
are not proto-go Product Intent.

This product-level execution boundary does not define a fixed universal
sequence of numbered stages. It also does not define the proto-go workflow
language, workflow artifact syntax, runtime API, invocation-input format,
execution-status vocabulary, persistence mechanism, or process topology.

## 0.12 Local procedural evolution and explicit procedural composition

`proto-go`'s implementation-to-publication procedure MUST support local
procedural evolution.

A procedural change whose semantic effect is local MUST require changes
proportional to that semantic effect, rather than unrelated changes across the
rest of the procedure merely because of how generic execution continuity is
realized by Prelock.

The effective business procedure MUST remain explicitly identifiable as a
composition of independently understandable procedural responsibilities rather
than emerging implicitly from distributed orchestration plumbing or generic
runtime mechanics.

The following consequences are normative:

* execution plumbing MUST NOT itself implicitly define the procedure;
* a procedural responsibility MUST NOT need unrelated knowledge of the complete
  end-to-end procedure merely to participate in it;
* adding, removing, replacing, reordering, or refining one procedural capability
  MUST NOT require unrelated procedural components to change solely to route
  control through that change;
* changing how a procedural capability is executed or continued MUST NOT
  unnecessarily redefine surrounding procedure semantics;
* genuinely global semantic constraints MAY legitimately couple multiple
  procedural responsibilities;
* incidental orchestration mechanics MUST NOT create equivalent global coupling;
* this requirement does not require `proto-go` to become a general-purpose
  workflow runtime or to reproduce Turnlock capabilities.

Explicit identifiability concerns how proto-go's business procedure is defined
and composed, not how Prelock realizes its execution mechanics. Generic
execution plumbing supplied by Prelock MUST NOT implicitly define the
proto-go workflow.

This Product Intent does not yet define:

* where the effective proto-go business procedure is defined;
* what its independently understandable procedural responsibilities are;
* which dependencies between them are semantically necessary;
* which dependencies exist only because of current orchestration plumbing;
* what minimum explicit composition model is required to make local procedural
  changes local;
* how proto-go's workflow requests execution forms from Prelock;
* whether the procedure is represented as stages, nodes, transitions,
  obligations, rules, capabilities, or another abstraction.

Those are downstream derivations forced by this Product Intent. They MUST be
derived rather than invented during implementation.

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

Managed authoring uses a proto-go-created temporary detached Git worktree bound
to one `ManagedContribution` and participating repository. That binding is a
subordinate managed authoring resource; it does not define
`ManagedContribution` identity and `ManagedContribution` must not be equated
with a worktree.

One readiness occurrence establishes the complete set of Repository
Publication Obligations applicable to its bound authored state and repository
participation. Those repository-local obligations may be satisfied
independently; the `ManagedContribution` establishes `PUBLISHED` only when an
effective readiness occurrence exists and all of its obligations are satisfied.

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

Its normal successful terminal condition requires the governing publication
outcome and satisfaction of all applicable proto-go-owned closure obligations.

A logical `proto-go` operation is not defined by the lifetime of one chat session,
agent process, operating-system process, or future execution-engine run.

## proto-go workflow

The implementation-to-publication workflow owned by proto-go. It owns proto-go's
business state, authority, obligations, semantically legal progression, requested
execution, interpretation of returned execution truth, and semantic completion.

Its execution is provided by Prelock. Its concrete representation
(stages, rules, obligations, nodes, state machine, DSL, functions, or another
abstraction) is not defined.

## ManagedContribution

The single logical implementation contribution owned by one logical `proto-go`
operation.

A `ManagedContribution` may contain managed authoring effects in one or more
repositories.

Its identity is not repository identity and is not defined by a branch,
worktree, session, agent, process, or mutable authoring surface.

The concrete representation of `ManagedContribution` identity is not yet
defined.

## Managed Authoring Worktree

The proto-go-created temporary detached Git worktree bound to one
`ManagedContribution` and participating repository for managed authoring.

The binding belongs to the contribution and repository rather than to a
conversational session, execution occurrence, or transient execution context.

Its filesystem path, naming, registry representation, cleanup implementation,
base-commit selection algorithm, and reconstruction mechanism are not defined.

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

## Repository Publication Obligation

A governing repository-local publication requirement established for one
readiness occurrence, one participating repository, the determinate authored
state bound for that repository, and the applicable governing publication
requirement.

Obligation identity and satisfaction are distinct from readiness identity.

Satisfaction is determined against authoritative publication reality.

A repository-local publication fact is not itself `ManagedContribution`
`PUBLISHED`, and partial obligation satisfaction is not partial proto-go
success.

Its serialization and persistence model are not defined by this specification.

## Governing publication outcome

The publication outcome required by the version-control policy governing the
contribution.

For a multi-repository `ManagedContribution`, reaching that outcome requires an
effective publication-authorizing readiness occurrence and authoritative
satisfaction of every Repository Publication Obligation established for that
occurrence.

Reaching that outcome is required for the normal successful terminal condition
of the logical `proto-go` operation, but it is not by itself sufficient while
applicable proto-go-owned closure obligations remain.

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

For routine Git versioning, the intended specialized workflow is proto-ruu.
proto-go supplies the applicable `WorkBoundary` and Git authority and retains
its own readiness, Repository Publication Obligation, and aggregate `PUBLISHED`
semantics.

Its concrete implementation and API are not yet selected by this specification.

## Prelock

The reusable execution substrate that provides generic workflow execution
continuity, control transfer, execution occurrence truth, and child-workflow
call/return for externally defined workflows.

Prelock does not know proto-go domain concepts such as
`ManagedContribution`, readiness, validation obligations, Repository
Publication Obligations, `PUBLISHED`, or managed worktrees.

Its API and implementation mechanisms are not proto-go Product Intent.

## proto-ruu

The specialized routine-Git-versioning workflow intended to be used by proto-go
for routine Git version-control progression within the `WorkBoundary` and Git
authority supplied by proto-go.

proto-ruu does not own proto-go publication semantics. The `WorkBoundary`
representation is not defined here.

## `/go` skill

The user-facing invocation surface through which a main-agent session begins or
continues a logical proto-go operation.

The invocation begins proto-go workflow execution or continues an applicable
existing proto-go workflow execution.

The `/go` skill is not the Product Intent authority; it must not redefine the
semantics established by the normative proto-go specification. Generic
execution continuity and continuation mechanics are provided by Prelock.

## Invocation Preflight

The `/go`-governed phase before Admission of a logical proto-go operation.

During Invocation Preflight, the main agent derives the best currently
available machine-readable invocation input from the user request,
authoritative context, and relevant established proto-go state.

Invocation Preflight may require execution realized by Prelock, including
mechanical execution, main-agent work, user interaction, or child-workflow
calls, before an admission-complete Launch Contract exists.

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

The Launch Contract may be established through pre-Admission proto-go
progression, including execution realized by Prelock.

The Launch Contract is the initial authority root of the admitted logical
proto-go operation.

It is not defined as equivalent to one concrete serialized document or one
persistence record.

# 4. Required properties and invariants

The following invariants are normative consequences of the accepted Product
Intent and accepted ADRs.

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

## PROTO-GO-INV-008 — Publication is the only normal successful terminal outcome — SUPERSEDED

**Status:** Superseded by ADR-013.

This invariant previously asserted that the governing publication outcome was
the normal successful terminal outcome of a logical `proto-go` operation, and
that `proto-go` must not report normal success until that outcome had been
reached.

ADR-013 preserves publication as mandatory for normal successful completion. It
supersedes the claim that publication itself necessarily exhausts all
proto-go-owned work: normal successful completion additionally requires all
applicable proto-go-owned closure obligations to be satisfied.

The preserved and corrected requirement is represented by
`PROTO-GO-INV-051`.

`PROTO-GO-INV-008` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

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

ADR-017 establishes a differently identified independence requirement for the
new execution boundary as `PROTO-GO-INV-060`; this identity remains superseded
and is not reactivated.

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

## PROTO-GO-INV-020 — One readiness occurrence forms one logical publication unit — SUPERSEDED

**Status:** Superseded by ADR-014.

This invariant previously treated the managed effects bound by one readiness
occurrence as one indivisible logical publication unit for governing publication
semantics.

ADR-014 replaces that indivisible publication unit with an aggregate set of
independently satisfiable Repository Publication Obligations. A proper subset
may reach its repository-local publication outcome while the
`ManagedContribution` remains not `PUBLISHED`.

The corrected requirement is represented by `PROTO-GO-INV-053`.

`PROTO-GO-INV-020` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-021 — No proper subset may independently reach governing publication — SUPERSEDED

**Status:** Superseded by ADR-014.

This invariant previously prohibited any proper subset of the managed effects
belonging to one logical publication unit from independently reaching its
governing publication outcome while the remainder had not.

ADR-014 explicitly permits proper subsets of repository-local publication
obligations to be satisfied while the `ManagedContribution` remains not
`PUBLISHED`.

The corrected requirement is represented by `PROTO-GO-INV-054`.

`PROTO-GO-INV-021` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-022 — Incapable routes must not begin irreversible partial publication — SUPERSEDED

**Status:** Superseded by ADR-014.

This invariant previously required a downstream route to preserve all-or-none
cross-repository governing publication semantics and prohibited beginning an
irreversible partial governing publication when a route could not do so.

ADR-014 removes cross-repository all-or-none publication visibility as a
Product Intent requirement. No route-capability requirement replaces it.

The corrected requirements are represented by `PROTO-GO-INV-054` and
`PROTO-GO-INV-055`.

`PROTO-GO-INV-022` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-023 — Main-agent skill orchestration — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required a proto-go operation to be initiated through
`/go` and required the `/go` skill to supply the procedure governing the main
agent's active proto-go progression.

ADR-017 assigns generic execution continuity and control transfer to
Prelock. `/go` remains the user-facing invocation, and the main agent
performs authored work when the proto-go workflow requests it, but the
main-agent-skill orchestration model is no longer proto-go Product Intent.

The invocation and workflow/runtime boundary is represented by
`PROTO-GO-INV-040`, `PROTO-GO-INV-059`, and `PROTO-GO-INV-061`.

`PROTO-GO-INV-023` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-024 — First procedural step is a terminating proto-go-script invocation — SUPERSEDED

**Status:** Superseded by ADR-008.

This invariant previously asserted that invocation of the proto-go script was
the first procedural step after `/go` invocation.

ADR-008 supersedes that ordering by establishing Invocation Preflight and
Admission before the first mechanical proto-go script transition.

The terminating-script model was later removed from proto-go Product Intent by
ADR-017. This identity remains historical and MUST NOT be reused.

`PROTO-GO-INV-024` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-025 — Active script execution cannot contain a suspended main-agent continuation — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously prohibited an active proto-go script invocation from
suspending into a main-agent continuation and later resuming the same script
execution, and prohibited the script from owning end-to-end orchestration.

The suspended-execution prohibition was a constraint on a specific execution
mechanism. ADR-017 removes that mechanism from proto-go Product Intent and
assigns generic control transfer and execution continuity to Prelock.

`PROTO-GO-INV-025` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

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

ADR-017 later superseded the script/artifact mechanism through which that
pre-Admission progression was realized; the business requirement that Admission
needs an admission-complete Launch Contract remains in `PROTO-GO-INV-039`.

`PROTO-GO-INV-030` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-031 — The Launch Contract is the initial authority root

**Status:** Amended by ADR-017; the semantic property is unchanged.

The admitted Launch Contract MUST form the initial authority root of the logical
proto-go operation.

Normative contracts, obligations, or semantic decisions used by proto-go during
the operation MUST derive from:

- authority represented by the admitted Launch Contract;
- authoritative facts resolved under that authority; or
- explicit additional authority obtained through an authorized continuation.

The main agent, the execution substrate, and downstream mechanical systems MUST
NOT invent missing semantic authority merely because progression requires it.

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

It does not by itself prevent pre-Admission execution during Invocation
Preflight. ADR-017 later removed the script-specific mechanism; the preserved
business fact is represented by `PROTO-GO-INV-062`.

The corrected semantics are represented by `PROTO-GO-INV-062` and
`PROTO-GO-INV-039`.

`PROTO-GO-INV-032` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-033 — First mechanical transition after admission is a fresh terminating script invocation — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required the first mechanical transition after
Admission to be a fresh terminating invocation of the proto-go script.

ADR-017 removes the proto-go script and terminating-invocation control model
from proto-go Product Intent. Post-Admission progression is realized by
Prelock under the execution forms requested by the proto-go workflow.

`PROTO-GO-INV-033` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-034 — Completed script invocation emits machine-readable continuation artifacts — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required each completed proto-go script invocation to
emit machine-readable Continuation Artifact information.

Continuation Artifacts are generic execution-continuity machinery. ADR-017
assigns that responsibility to Prelock.

`PROTO-GO-INV-034` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-035 — Continuation artifacts do not hold procedural authority — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously governed the relationship between Continuation
Artifacts, Continuation Policy, and main-agent procedural authority.

Those concepts are no longer proto-go-owned execution machinery.
`PROTO-GO-INV-061` now establishes that proto-go owns its workflow progression
decisions and that generic execution truth does not determine proto-go
semantics.

`PROTO-GO-INV-035` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-036 — Main-agent continuation occurs only after script termination — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required any main-agent continuation caused by script
results to occur only after that invocation terminated, and prohibited modeling
continuation as suspension into the main agent followed by resumption of the
same script invocation.

Those requirements constrained the removed proto-go script mechanism. Generic
control-transfer ordering and continuation are provided by Prelock.

`PROTO-GO-INV-036` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-037 — Mechanical re-entry uses a fresh script invocation — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required mechanical re-entry to use a fresh script
invocation and prohibited treating a later invocation as resumption of an
earlier terminated invocation.

Fresh script re-entry was a constraint of the removed proto-go execution
mechanism. Execution re-entry and continuation are provided by Prelock.

`PROTO-GO-INV-037` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-038 — Pre-admission progression may invoke the proto-go script — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously permitted the proto-go script to be invoked during
Invocation Preflight before an admission-complete Launch Contract existed, and
required each such invocation to remain terminating and return Continuation
Artifact information.

The script-specific form is superseded. The preserved business fact — that
pre-Admission proto-go progression may use execution before Launch Contract
completeness — is represented by `PROTO-GO-INV-062`.

`PROTO-GO-INV-038` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-039 — Admission requires a Launch Contract established through authoritative progression

**Status:** Amended by ADR-017; the semantic property is unchanged.

A logical proto-go operation MUST NOT be admitted until an admission-complete
machine-readable Launch Contract has been established.

The Launch Contract MAY be established through repeated pre-Admission proto-go
progression, including execution realized by Prelock, authoritative
context resolution, and user clarification where required.

The Launch Contract MUST represent sufficiently resolved implementation intent,
governing authority basis, and launch premises for Admission.

Neither the main agent nor the execution substrate MAY invent missing semantic
authority in order to make the Launch Contract complete.

This invariant does not define which component stores the Launch Contract, its
schema, serialization, persistence representation, or versioning model.

## PROTO-GO-INV-040 — `/go` re-entry continues an existing proto-go objective

**Status:** Amended by ADR-017; the semantic property is unchanged.

A `/go` invocation that continues an existing proto-go objective MUST continue
the relevant existing progression rather than create a distinct logical
proto-go objective merely because `/go` was invoked again.

Before Admission, such continuation proceeds within the relevant pre-Admission
progression without retroactively creating a `ManagedContribution`.

After Admission, it MUST preserve the same logical proto-go operation and
`ManagedContribution`.

This invariant does not define how the relevant progression is identified,
correlated, rediscovered, persisted, or transported; those are provided by
Prelock.

## PROTO-GO-INV-041 — Artifact-driven progression spans the objective through publication — SUPERSEDED

**Status:** Superseded by ADR-013.

This invariant previously asserted that the normal successful terminal
condition of the artifact-driven proto-go progression was the governing
publication outcome.

ADR-013 extends artifact-driven progression through applicable proto-go-owned
post-publication closure obligations. Publication remains required, but normal
successful completion is reached only after publication and all applicable
closure obligations have been satisfied.

The corrected continuation semantics are represented by
`PROTO-GO-INV-064`.

`PROTO-GO-INV-041` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-042 — Control transfer requires sufficient authoritative Progression Context — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required sufficient machine-readable authoritative
Progression Context at every main-agent/script control transfer.

Progression Context is generic execution-continuity state. ADR-017 assigns
explicit execution continuity and control-transfer context to Prelock.
Proto-go retains ownership of its own business state and authority through
`PROTO-GO-INV-061`.

`PROTO-GO-INV-042` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-043 — Managed authoring uses dedicated proto-go-created Git worktrees

For every participating repository in which an admitted `ManagedContribution`
requires managed authored mutation, proto-go MUST automatically establish a
dedicated Git worktree for that `ManagedContribution` and repository before the
first such mutation occurs.

Managed authored mutation MUST NOT occur in the invoking checkout, an ordinary
user checkout, or another `ManagedContribution`'s managed authoring worktree.

Distinct concurrently authored `ManagedContribution` instances MUST NOT share
the same managed authoring worktree.

A repository dynamically entering the `ManagedContribution`'s authoring scope
MUST receive its managed worktree before its first managed authored mutation.

This invariant does not define worktree path, naming, registry representation,
or provisioning command sequence.

## PROTO-GO-INV-044 — Managed authoring worktrees are detached at determinate commits

Each managed authoring worktree MUST be provisioned from a determinate Git
commit.

The managed authoring worktree MUST use detached HEAD while it serves as
proto-go's managed authoring surface.

Managed authoring MUST NOT rely on mutation of an ordinary checked-out branch
as its authoring surface.

This invariant does not define how the authoritative base commit is selected or
how downstream publication later realizes the authored state through refs,
branches, commits, or another version-control mechanism.

## PROTO-GO-INV-045 — Managed worktree bindings follow the contribution lifecycle, not the session

**Status:** Amended by ADR-017; the semantic property is unchanged.

The authoritative managed-worktree binding belongs to the relevant
`ManagedContribution` and participating repository rather than to a
conversational session, execution occurrence, or transient execution context.

Session replacement MUST NOT by itself create another managed authoring
worktree for the same `ManagedContribution` and repository.

The managed worktree MUST remain available as required across execution-
occurrence termination, session replacement, `READY FOR HANDOFF`, publication
attempts, readiness fencing, authored correction, revalidation, and later
readiness occurrences.

After `PUBLISHED` is established and the worktree is no longer required for
authored progression, proto-go MUST attempt its normal automatic retirement and
removal as a proto-go-owned closure obligation.

Failure of that cleanup MUST NOT invalidate `PUBLISHED` and MUST be surfaced as
an actionable proto-go condition rather than silently ignored.

No incidental event such as session loss, process loss, elapsed time, `READY`,
or execution-occurrence termination MAY by itself authorize destruction of the
worktree.

This invariant does not define abandonment or garbage-collection semantics.

## PROTO-GO-INV-046 — Invocation and continuation are session-agnostic — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required proto-go invocation and continuation to be
session-agnostic, including continuation from a later eligible main-agent
session without creating a new objective, `ManagedContribution`, or authoring
worktree, while preserving authoritative Progression Context.

Generic session-transfer and continuation mechanics belong to Prelock.
The preserved proto-go business consequences remain normative through
`PROTO-GO-INV-011`, `PROTO-GO-INV-040`, and `PROTO-GO-INV-045`.

`PROTO-GO-INV-046` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-047 — Distinct proto-go progressions may advance concurrently

**Status:** Amended by ADR-017; the semantic property is unchanged.

Distinct proto-go progressions MUST be capable of advancing concurrently.

proto-go MUST NOT require global serialization of otherwise-independent `/go`
progressions merely because they affect the same repository or because another
progression is active.

This invariant does not guarantee conflict-free later integration or
publication.

## PROTO-GO-INV-048 — One progression has at most one independent active main-agent controller — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required at most one independent active main-agent
procedural controller per proto-go progression.

Control ownership and prevention of contradictory independent progression are
generic runtime concerns. ADR-017 assigns control ownership and session
coordination to Prelock.

`PROTO-GO-INV-048` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-049 — `/go` procedural instructions are resolved incrementally — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required `/go` procedural instructions to be resolved
incrementally from a bounded bootstrap rather than requiring the complete
instruction corpus upfront.

That requirement existed to support the removed main-agent-skill orchestration
model. Instruction loading and execution continuity belong to Prelock and
the surrounding harness, and no independent proto-go domain requirement
depends on them.

`PROTO-GO-INV-049` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-050 — Actionable mechanical conditions preserve the objective through main-agent continuation — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required an actionable mechanical condition detected
by a terminating script invocation to preserve the same proto-go objective
through an authorized main-agent continuation, and constrained the script
termination and retry mechanics.

The identity is mechanism-shaped. The preserved semantic core — that actionable
conditions preserve the same proto-go objective while authorized progress
remains possible — is represented by `PROTO-GO-INV-063`.

`PROTO-GO-INV-050` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-051 — Publication is necessary but not sufficient for normal successful completion

The governing publication outcome MUST be established before a logical proto-go
operation may report normal successful completion.

`READY FOR HANDOFF`, implementation completion, validation completion, commits,
or publication attempts remain insufficient.

An established `PUBLISHED` fact MUST remain true even if a later proto-go-owned
closure obligation fails.

A logical proto-go operation MUST NOT report normal successful completion while
an applicable proto-go-owned closure obligation remains unsatisfied.

Therefore `PUBLISHED` is necessary for normal successful completion but is not
by itself sufficient when such obligations remain.

This invariant supersedes `PROTO-GO-INV-008`.

## PROTO-GO-INV-052 — Artifact-driven progression continues through proto-go-owned closure obligations — SUPERSEDED

**Status:** Superseded by ADR-017.

This invariant previously required the `/go` artifact-driven progression to
continue through proto-go-owned closure obligations using Progression Context,
Continuation Artifact, terminating-script, Continuation Policy, and
fresh-reentry semantics.

The identity is mechanism-shaped. The preserved semantic core — that proto-go
progression continues through closure obligations without reverting `PUBLISHED`
and reaches normal successful completion only when publication and all
applicable obligations are satisfied — is represented by `PROTO-GO-INV-064`.

`PROTO-GO-INV-052` is retained only to preserve invariant identity history.

It is no longer a normative requirement and its identifier MUST NOT be reused
for a different invariant.

## PROTO-GO-INV-053 — One readiness occurrence defines aggregate repository publication obligations

Each publication-authorizing `READY FOR HANDOFF` occurrence MUST establish the
complete set of Repository Publication Obligations applicable to the
repository participation and determinate authored state bound by that
readiness occurrence.

Those obligations collectively define the publication-completion condition
for that readiness occurrence.

This invariant does not require repository-local publication outcomes to occur
atomically and does not define the concrete representation or identifier of an
obligation.

## PROTO-GO-INV-054 — Repository publication obligations may be satisfied independently

Repository Publication Obligations belonging to one readiness occurrence MAY
reach their governing repository-local publication outcomes independently and
at different times.

Satisfaction of a proper subset MUST NOT by itself establish `PUBLISHED` for
the `ManagedContribution`.

A repository-local publication outcome MUST NOT be treated as a partial
successful completion of the logical proto-go operation.

This invariant does not prescribe sequential or parallel publication.

## PROTO-GO-INV-055 — PUBLISHED requires one effective readiness with all publication obligations satisfied

A `ManagedContribution` MUST NOT establish `PUBLISHED` unless an effective
publication-authorizing readiness occurrence exists and every Repository
Publication Obligation established for that readiness occurrence is
authoritatively satisfied.

A retired readiness occurrence MUST NOT establish `PUBLISHED` even if every
repository-local publication outcome formerly associated with it later becomes
satisfied.

This invariant does not define how effective readiness authority or obligation
satisfaction is represented.

## PROTO-GO-INV-056 — Each new readiness evaluates its own publication obligations against current authoritative reality

A later readiness occurrence MUST establish its own Repository Publication
Obligations for its own bound authored state and governing publication
requirements.

Publication-obligation satisfaction MUST NOT be inherited merely because an
earlier readiness occurrence had a similar obligation.

If current authoritative publication reality already satisfies the later
readiness occurrence's exact repository-local publication requirement, that
later obligation MAY already be satisfied without repeating the publication
mechanically.

A prior publication fact MUST NOT satisfy a later obligation when the bound
authored state or governing publication requirement differs materially.

## PROTO-GO-INV-057 — Readiness retirement forbids new repository publication initiation

Before managed authored mutation resumes after a publication-authorizing
readiness occurrence, that readiness occurrence MUST be retired as required by
the existing authored-resumption semantics.

After retirement, no new repository-local publication operation MAY be
initiated under authority derived from that retired readiness occurrence.

The retired readiness occurrence MUST remain incapable of establishing the
`ManagedContribution`'s `PUBLISHED` fact and MUST NOT later be reactivated.

This invariant does not require cancellation of repository-local publication
operations already originated before retirement.

## PROTO-GO-INV-058 — Already-originated repository publication may complete after readiness retirement

Retirement of a readiness occurrence MUST NOT require proto-go to cancel,
fence, roll back, or wait for repository-local publication operations that
were already originated under that readiness occurrence before retirement.

Such already-originated operations MAY complete after retirement.

Any repository-local publication outcome thereby authoritatively established
MUST remain a valid historical publication fact.

Such a fact MUST NOT by itself restore the retired readiness occurrence's
authority or permit that retired readiness occurrence to establish
`ManagedContribution` `PUBLISHED`.

This invariant does not prohibit an implementation from opportunistically
cancelling an in-flight operation when otherwise safe; cancellation is simply
not a Product Intent prerequisite for authored resumption.

## PROTO-GO-INV-059 — Proto-go workflow execution is provided by Prelock

Proto-go is the implementation-to-publication workflow that owns its domain
semantics and progression decisions.

Generic execution continuity and control-transfer mechanics required to execute
that workflow — including mechanical execution, main-agent continuation, and
child-workflow execution and structured return — are provided by Prelock.

Proto-go MUST NOT independently own generic workflow-execution mechanisms as
part of its Product Intent merely because earlier proto-go generations emulated
them.

This invariant does not select a runtime API, workflow language, persistence
model, execution-status vocabulary, or process topology.

## PROTO-GO-INV-060 — Proto-go business semantics are independent of the execution substrate

Proto-go's Product Intent and business semantics MUST NOT depend on the concrete
generic execution mechanisms used to execute proto-go's workflow.

Changing or replacing the execution substrate provided by Prelock MUST NOT
by itself redefine proto-go domain meaning, canonical terminology, lifecycle
facts, or domain obligations.

The historical `PROTO-GO-INV-012` identity remains superseded and is not reused
for this requirement.

This invariant does not select a runtime API, protocol, representation, or
process topology.

## PROTO-GO-INV-061 — Proto-go owns its workflow progression decisions

Proto-go MUST own the interpretation of its own business state, the authority
available to it, the business obligations that remain, which progression is
semantically legal, which execution it requests next, how returned execution
truth affects proto-go business state, and when proto-go is semantically
complete.

Execution truth or execution outcomes provided by Prelock MUST NOT by
themselves establish or negate proto-go domain transitions such as validation
completion, readiness, publication-obligation satisfaction, `PUBLISHED`, or
normal successful completion.

## PROTO-GO-INV-062 — Pre-admission progression may use execution before Launch Contract completeness

During Invocation Preflight, proto-go's business progression may require
execution before an admission-complete Launch Contract exists.

Missing Admission information or authority MUST prevent Admission, but MUST NOT
by itself prohibit proto-go's pre-Admission progression.

This invariant does not define the execution mechanisms, channels, or
representations used for that progression.

## PROTO-GO-INV-063 — Actionable conditions preserve the same proto-go objective

When proto-go establishes a business or mechanical condition that prevents
normal completion, and authorized progress toward the same implementation
objective remains possible, the same proto-go objective MUST remain in force.

Resolving the condition MUST NOT create a new implementation request, logical
proto-go operation, or `ManagedContribution`.

This invariant does not define the execution form by which the required work is
performed.

## PROTO-GO-INV-064 — Progression continues through proto-go-owned closure obligations

Proto-go's business progression MUST remain capable of continuing after the
governing publication outcome when proto-go-owned closure obligations remain.

`PUBLISHED` MUST NOT be reverted merely because a later closure operation fails.

Normal successful completion is reached only after `PUBLISHED` has been
established and all applicable proto-go-owned closure obligations have been
satisfied.

This invariant does not define a lifecycle-state representation or execution
mechanism.

## PROTO-GO-INV-065 — Publication semantics remain proto-go-owned when routine Git progression is delegated to proto-ruu

Proto-go MUST retain ownership of `ManagedContribution` readiness, Repository
Publication Obligations, aggregate `PUBLISHED` semantics, and publication as
necessary for normal successful completion.

When proto-go requires specialized routine Git versioning progression, it MUST
supply the applicable `WorkBoundary` and Git authority to proto-ruu, invoked as
a child workflow through Prelock.

Proto-go MUST NOT absorb proto-ruu's internal Git semantics, and proto-ruu MUST
NOT own or redefine proto-go publication semantics.

This invariant does not define `WorkBoundary` representation, delegation
granularity, child-call syntax, or result shape.

# 5. Lifecycle semantics

The proto-go workflow progression begins before Admission and continues through
the governing publication outcome:

```text
/go invocation
        ↓
pre-Admission proto-go workflow progression
        ↺
admission-complete Launch Contract
        ↓
Admission
        ↓
ManagedContribution
        ↓
managed authoring / validation / business progression
        ↺
READY
        ↓
version-control progression through proto-ruu as applicable
        ↺
possible correction / revalidation / later READY
        ↺
PUBLISHED
        ↓
applicable proto-go-owned closure obligations
        ↺ if repairable
        ↓
normal successful completion
```

`READY FOR HANDOFF` is not normal termination of the proto-go workflow
progression.

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
version-control progression through proto-ruu as applicable
        ↓
governing publication outcome
        ↓
applicable proto-go-owned closure obligations
        ↓
normal successful completion
```

`READY FOR HANDOFF` is an internal durable lifecycle boundary.

The governing publication outcome is required for normal successful terminal
completion. Normal successful completion additionally requires every applicable
proto-go-owned closure obligation, such as managed worktree cleanup, to be
satisfied.

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

Before that authored mutation may resume, the prior readiness occurrence must
first be retired from establishing the `ManagedContribution`'s global
`PUBLISHED` fact, and new repository-local publication initiation under it must
be prohibited:

```text
READY #1
        ↓
downstream progression cannot continue mechanically
        ↓
authored correction required
        ↓
retire READY #1 global publication-completion authority
        ↓
prohibit new repository-local publication initiation under READY #1
        ↓
authored mutation may resume
        ↓
state S2
        ↓
new applicable readiness required
```

The historical fact that `READY #1` was validly established remains true.

What changes is its authority to reach publication.

Already-originated repository-local publication operations under `READY #1` may
complete after retirement without being cancelled, fenced, rolled back, or
awaited as a precondition to authored resumption. Any repository-local
publication outcome they authoritatively establish remains a valid historical
publication fact, but it does not restore `READY #1`'s authority.

Retirement does not erase readiness. It prevents the retired readiness
occurrence from establishing the `ManagedContribution`'s global `PUBLISHED`
fact, forbids new publication initiation under it, and is never reversed.

At any time, no more than one readiness occurrence of the same
`ManagedContribution` may retain effective authority to reach the governing
publication outcome.

This requirement does not define a concrete lifecycle-state representation or
fencing mechanism.

The logical `ManagedContribution` itself remains the same unless some separate
future semantic rule says otherwise.

For example:

```text
READY #1:
  A@SA
  B@SB
  C@SC

publication:
  A@SA → satisfied
  B@SB → blocked
  C@SC → in flight

B requires authored correction
↓
retire READY #1
↓
READY #1 may initiate no new publication work
↓
author B:
  SB → SB2

while authoring:
  C@SC → published

current publication reality:
  A@SA = published
  C@SC = published

↓
validate resulting complete contribution state
↓
READY #2:
  A@SA
  B@SB2
  C@SC

evaluate READY #2 obligations against current reality:

A@SA → already satisfied, if exact requirement matches
B@SB2 → unsatisfied
C@SC → already satisfied, if exact requirement matches

↓
publish B@SB2
↓
all publication obligations of effective READY #2 satisfied
↓
ManagedContribution PUBLISHED
```

`READY #2` does not inherit `READY #1`'s obligations. It independently
evaluates its own Repository Publication Obligations against current
authoritative publication reality.

# 6. Isolation semantics

Managed authored mutation requires isolated ordinary mutable authoring
surfaces, as required by `PROTO-GO-INV-005`.

This specification selects dedicated Git worktrees as the managed authoring
isolation mechanism.

For each participating repository in which an admitted `ManagedContribution`
requires managed authored mutation, proto-go provisions a dedicated temporary
Git worktree before the first managed authored mutation in that repository.

The worktree belongs to:

```text
ManagedContribution × participating repository
```

It does not belong to a conversational session, a transient execution
occurrence, or a generic runtime controller.

The managed authoring worktree is created from a determinate Git commit and
uses detached HEAD while it serves as proto-go's managed authoring surface.

Managed authoring must remain detached from an ordinary checked-out branch.

The invoking checkout, the user's ordinary checkout, and another
`ManagedContribution`'s managed authoring worktree are not managed authoring
surfaces.

Distinct concurrently authored `ManagedContribution` instances must not share
the same managed authoring worktree.

A repository dynamically added to an existing `ManagedContribution` receives
its own managed worktree before the first managed authored mutation in that
repository.

The worktree binding follows the contribution and repository rather than a
conversational session or execution context. Session replacement must not by
itself create a replacement worktree for the same `ManagedContribution` and
repository.

This worktree selection strengthens the isolation requirement expressed by
`PROTO-GO-INV-005`; it does not supersede that invariant.

Git-worktree isolation provides managed authoring isolation only. It does not by
itself solve every shared-Git-repository concurrency concern such as refs,
downstream publication, or convergence. Those downstream concerns are not
assigned to proto-go merely because worktrees are selected for authoring
isolation.

The worktree filesystem path, naming, registry representation, base-commit
selection algorithm, reconstruction mechanism, forced-removal policy, and
garbage-collection semantics remain undecided.

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
   publication outcome has been reached and all applicable proto-go-owned
   closure obligations have been satisfied.

The exact handoff payload, API, identifier mapping, transport, process boundary,
repository-local decomposition, downstream implementation, and publication-route
semantics remain undecided.

Each downstream handoff is associated with a specific readiness occurrence.

The downstream system must progress the authored state bound by that readiness
occurrence rather than an authored target that continues changing underneath the
handoff.

The readiness occurrence establishes the complete set of Repository
Publication Obligations applicable to its bound authored state and repository
participation.

Repository-local publication outcomes may occur independently and at different
times. A proper subset of those obligations may be satisfied while the
`ManagedContribution` remains not `PUBLISHED`.

The `ManagedContribution` may establish `PUBLISHED` only when an effective
publication-authorizing readiness occurrence exists and every Repository
Publication Obligation established for it is authoritatively satisfied.

If downstream progression for a readiness occurrence requires authored
correction or convergence, proto-go must retire that readiness occurrence's
publication-completion authority before authored mutation resumes and must
prohibit new repository-local publication initiation under it.

After retirement:

```text
historical readiness
→ remains true

future ability to establish ManagedContribution PUBLISHED
→ absent

new repository-local publication initiation under that readiness
→ forbidden
```

Repository-local publication operations already originated before retirement
may complete after retirement. Their repository-local publication facts remain
authoritative, but they cannot restore the retired readiness occurrence's
authority or establish the `ManagedContribution`'s `PUBLISHED` fact.

A later authored state that becomes eligible for downstream progression requires
a new applicable readiness occurrence.

# 9. Failure, interruption, and recovery semantics

The following failure, interruption, and recovery semantics are currently
established:

* transient session or execution loss does not by itself terminate a proto-go
  objective or change proto-go business truth;
* incidental events such as session loss, process loss, execution-occurrence
  termination, elapsed time, or `READY` must not by themselves authorize
  destruction of a managed authoring worktree;
* an actionable condition preserves the same proto-go objective while
  authorized progress remains possible;
* a post-`PUBLISHED` closure failure preserves the established `PUBLISHED` fact
  and may continue through proto-go progression.

Generic execution recovery and session re-entry are provided by Prelock
and are not selected here.

Abandonment semantics and a complete recovery taxonomy are not yet defined.

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

Repository-local publication outcomes for one readiness occurrence may occur
concurrently and independently, provided each operates on the authored state
bound for its repository. Independent repository-local publication outcomes do
not by themselves establish the `ManagedContribution`'s `PUBLISHED` fact.

Distinct proto-go progressions may run concurrently. That inter-progression
concurrency is permitted and ordinary. Proto-go must not require global
serialization of otherwise-independent proto-go progressions.

Generic control ownership and prevention of contradictory independent
progression belong to Prelock. Proto-go retains the domain requirements
that distinct contributions preserve mutable-authoring isolation and that
distinct progressions are not globally serialized.

# 11. Harness-integration boundary

proto-go is invoked within a main-agent session through the user-facing `/go`
surface.

`/go` begins proto-go workflow execution or continues an applicable existing
proto-go workflow execution.

The main agent may perform authored work when the proto-go workflow requests a
main-agent continuation. It is not the global workflow orchestrator.

Generic execution continuity, control transfer, session re-entry, and
instruction/execution-continuity mechanics are provided by Prelock and
the surrounding harness. They are not proto-go Product Intent.

This product-level boundary does not make all surrounding harness policy part
of proto-go.

In particular, the surrounding harness's permission-enforcement policy about
whether implementation is allowed outside proto-go remains external to
proto-go Product Intent.

The concrete skill-discovery, installation, harness, process, and runtime
mechanisms remain undecided.

# 12. Version-control-system boundary

proto-go owns readiness, Repository Publication Obligations, aggregate
`PUBLISHED` semantics, and publication as necessary for normal successful
completion.

For specialized routine Git versioning progression, proto-go supplies the
applicable `WorkBoundary` and Git authority to proto-ruu, invoked as a child
workflow through Prelock, and interprets the returned versioning outcome
under its own publication semantics.

The version-control progression remains a distinct responsibility. proto-go
does not absorb proto-ruu's internal Git semantics, and proto-ruu does not own
proto-go publication semantics.

The exact handoff payload, API, identifier mapping, transport, `WorkBoundary`
representation, delegation granularity, child-call syntax, result shape, and
route-specific publication semantics remain undecided.

# 13. Architectural implications

Not yet derived.

Implementation pressure must not silently fill these sections.
