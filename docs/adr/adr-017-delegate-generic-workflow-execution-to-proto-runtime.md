# ADR-017: Delegate generic workflow execution to proto-runtime

- Status: Accepted
- Date: 2026-09-24

## Context

proto-go's execution model accumulated generic workflow-execution machinery
across ADR-006, ADR-008, ADR-009, ADR-011, ADR-012, and ADR-013: a
main-agent-skill procedure, a required terminating proto-go script primitive,
Continuation Artifacts, Continuation Policy, Progression Context,
main-agent/script control-transfer semantics, fresh script re-entry,
single-active-controller rules, and incremental instruction loading.

That machinery was introduced because proto-go had to operate before a reusable
execution substrate existed. proto-go was therefore responsible for emulating
generic execution continuity inside its own Product Intent.

A reusable execution substrate now exists as `proto-runtime`. It owns generic
execution continuity, control transfer, execution occurrence truth, and
child-workflow call/return for externally defined workflows, without knowing
workflow domain concepts.

The product problem is therefore no longer:

```text
proto-go must define how generic execution continues
```

but:

```text
proto-go must own an implementation-to-publication workflow
whose generic execution is provided by proto-runtime
```

The product owner has resolved the rescoping.

One accepted statement is explicitly reversed by this decision. ADR-006
asserted that the main-agent skill and terminating-script execution model was
"a product property of proto-go, not a temporary implementation limitation".
That assertion no longer holds: the removed machinery was a pre-proto-runtime
execution emulation, not part of the correct product boundary.

## Decision

proto-go is no longer responsible for its own generic execution/orchestration
machinery.

```text
proto-go
= implementation-to-publication workflow
= owns domain semantics and progression decisions

proto-runtime
= reusable execution substrate
= owns generic execution continuity and control-transfer mechanics
```

The governing boundary is:

```text
proto-go decides WHAT progression is semantically required

proto-runtime preserves and realizes HOW the declared workflow execution
continues across execution boundaries
```

proto-go MUST carry almost entirely implementation-to-publication business
semantics. It MUST NOT independently own generic runtime mechanisms merely
because earlier proto-go generations needed to emulate them before
proto-runtime existed.

Conceptually:

```text
/go
 ↓
proto-go workflow
 ↓
proto-runtime
 ├─ mechanical execution
 ├─ main-agent continuation
 └─ child-workflow execution / structured return
```

The exact invocation/runtime API remains undecided.

### Workflow-owned progression

proto-go owns:

```text
what its current business state means
what authority is available
which business obligations remain
which progression is semantically legal
which execution is requested next
how returned execution truth affects proto-go business state
when proto-go is semantically complete
```

proto-runtime does not decide:

```text
validation complete → READY
publication blockage → authored correction
new authored state → revalidation required
all publication obligations satisfied → PUBLISHED
PUBLISHED + closure complete → proto-go success
```

Those remain proto-go semantics.

### Target invocation and execution relationship

Conceptually:

```text
user invokes /go
        ↓
proto-go WorkflowExecution begins or applicable existing execution is continued
        ↓
proto-go evaluates its own authoritative workflow/domain state
        ↓
proto-go determines semantically legal next progression
        ↓
proto-runtime realizes the requested execution form
        ↓
execution truth / child result becomes available
        ↓
proto-go interprets it according to proto-go semantics
        ↺
```

The proto-go workflow may currently request execution such as mechanical
execution, main-agent continuation, or a child workflow call to proto-ruu.
The main agent may perform authored work when the proto-go workflow requests
it. The main agent is not the global workflow orchestrator merely because it
performs an agentic continuation.

This is conceptual Product Intent, not an API or workflow language.

### proto-ruu relationship

proto-ruu is the specialized routine-Git-versioning workflow intended to be
used by proto-go.

```text
proto-go workflow
      │
      │ supplies applicable WorkBoundary
      │ + applicable Git authority
      ▼
proto-ruu workflow
      │
      ▼
Git
```

When proto-go requires specialized routine Git version-control progression, its
workflow may invoke proto-ruu as a child workflow through proto-runtime.

The ownership boundary remains explicit:

```text
proto-go owns:
ManagedContribution
readiness
validation
Repository Publication Obligations
aggregate PUBLISHED semantics
implementation-to-publication lifecycle

proto-ruu owns:
routine Git versioning progression within the supplied WorkBoundary

proto-runtime owns:
generic workflow execution continuity,
control transfer,
execution occurrence truth,
and child-workflow call/return
```

This ADR does not decide how many proto-ruu calls one readiness occurrence
causes, whether one call spans all repositories or one publication obligation,
the exact WorkBoundary representation, the exact child-call syntax, or the
exact result shape. Those require later derivation.

## Classification of affected ADRs

The expected classification was verified against the accepted documents.

### ADR-006 — superseded (execution model)

ADR-006 established main-agent skill orchestration and terminating script
execution as proto-go Product Intent.

Superseded: the required main-agent-skill procedural orchestration, the
terminating proto-go script as a required execution primitive, the
main-agent/script control-transfer model, and the no-suspension rule stated as
proto-go Product Intent.

Preserved: `/go` remains the user-facing invocation, and the main agent may
perform authored work when the proto-go workflow requests it.

ADR-006's supersession of `PROTO-GO-INV-012` is unaffected. That identity
remains superseded and is not reactivated.

### ADR-008 — narrowed

Preserved: Invocation Preflight, the Admission boundary, the Launch Contract as
the initial authority root, and the fail-closed Admission rule.

Superseded: the terminating-script first transition, Continuation Artifact,
Continuation Policy, fresh-invocation control loop, and their control-transfer
mechanics.

ADR-008's supersession of `PROTO-GO-INV-024` remains historical.

### ADR-009 — narrowed

Preserved: state/authority-driven progression rather than a fixed universal
stage pipeline; pre-Admission progression; re-entry continuing the same logical
objective; `READY FOR HANDOFF` as intermediate; progression through
publication; the requirement that correct progression not depend on
conversational memory, now satisfied by proto-runtime's explicit execution
continuity.

Superseded: Progression Context as a proto-go-owned generic runtime context,
and the script/artifact/fresh-invocation runtime mechanics.

ADR-009's supersession of `PROTO-GO-INV-030` and `PROTO-GO-INV-032` remains
historical.

### ADR-011 — narrowed

Preserved: distinct proto-go operations may coexist and progress concurrently;
proto-go business identity survives transient session changes; a session change
does not create a new objective, `ManagedContribution`, or managed authoring
worktree.

Superseded: generic session-transfer mechanics and the single-active-
main-agent-controller rule as proto-go runtime machinery.

### ADR-012 — superseded

Incremental `/go` instruction loading was required only to support the
main-agent-skill orchestration and artifact-driven progression being removed
here. No independent proto-go domain requirement depends on it. Instruction
loading and execution continuity belong to proto-runtime and the surrounding
harness.

### ADR-013 — narrowed

Preserved: actionable conditions preserve the same proto-go objective while
authorized progress remains possible; `PUBLISHED` is authoritative historical
truth; normal successful completion requires publication plus all applicable
proto-go-owned closure obligations.

Superseded: artifact/script/main-agent continuation as the mechanism of that
semantic continuation.

ADR-013's supersession of `PROTO-GO-INV-008` and `PROTO-GO-INV-041` remains
historical.

### ADR-016 — preserved, relationship updated

ADR-016's core requirement is preserved: proto-go's business procedure must
support local procedural evolution, and the effective procedure must remain
explicitly identifiable as a composition of independently understandable
procedural responsibilities.

Its relationship to the execution substrate is now:

```text
proto-go
→ explicitly composable business workflow

proto-runtime
→ generic execution substrate
```

The effective proto-go procedure MUST NOT emerge implicitly from runtime
plumbing. Generic execution plumbing supplied by proto-runtime must not
implicitly define the proto-go workflow.

ADR-016's non-decisions remain non-decisions. This ADR does not select a step
abstraction, workflow graph, state-machine representation, DSL, function
decomposition, or other concrete workflow representation.

## Invariant classification

Superseded by this ADR:

```text
PROTO-GO-INV-023  main-agent skill orchestration
PROTO-GO-INV-025  active script execution cannot contain a suspended
                  main-agent continuation
PROTO-GO-INV-033  first mechanical transition after admission is a fresh
                  terminating script invocation
PROTO-GO-INV-034  completed script invocation emits machine-readable
                  continuation artifacts
PROTO-GO-INV-035  continuation artifacts do not hold procedural authority
PROTO-GO-INV-036  main-agent continuation occurs only after script termination
PROTO-GO-INV-037  mechanical re-entry uses a fresh script invocation
PROTO-GO-INV-038  pre-admission progression may invoke the proto-go script
PROTO-GO-INV-042  control transfer requires sufficient authoritative
                  Progression Context
PROTO-GO-INV-046  invocation and continuation are session-agnostic
PROTO-GO-INV-048  one progression has at most one independent active
                  main-agent controller
PROTO-GO-INV-049  /go procedural instructions are resolved incrementally
PROTO-GO-INV-050  actionable mechanical conditions preserve the objective
                  through main-agent continuation
PROTO-GO-INV-052  artifact-driven progression continues through proto-go-owned
                  closure obligations
```

Amended by this ADR without changing the invariant's semantic property:

```text
PROTO-GO-INV-031  Launch Contract authority root
PROTO-GO-INV-039  Admission requires a Launch Contract established through
                  authoritative progression
PROTO-GO-INV-040  /go re-entry continues an existing proto-go objective
PROTO-GO-INV-045  managed worktree bindings follow the contribution lifecycle
PROTO-GO-INV-047  distinct proto-go progressions may advance concurrently
```

The amendments remove obsolete execution-mechanism coupling; they do not
assign a materially different meaning to those identities.

Unaffected domain invariants remain normative, including INV-001 through
INV-011, INV-013 through INV-019, INV-026 through INV-029, INV-043, INV-044,
INV-051, and INV-053 through INV-058.

New invariants introduced to keep the normative set coherent:

```text
PROTO-GO-INV-059  proto-go workflow execution is provided by proto-runtime
PROTO-GO-INV-060  proto-go business semantics are independent of the execution
                  substrate
PROTO-GO-INV-061  proto-go owns its workflow progression decisions
PROTO-GO-INV-062  pre-Admission progression may use execution before Launch
                  Contract completeness
PROTO-GO-INV-063  actionable conditions preserve the same proto-go objective
PROTO-GO-INV-064  progression continues through proto-go-owned closure
                  obligations
PROTO-GO-INV-065  publication semantics remain proto-go-owned when routine Git
                  progression is delegated to proto-ruu
```

The historical `PROTO-GO-INV-012` identity remains superseded. The
independence requirement reintroduced at the new product boundary receives the
new identity `PROTO-GO-INV-060`.

`PROTO-GO-INV-050` and `PROTO-GO-INV-052` are superseded because their
identities are mechanism-shaped ("through main-agent continuation",
"artifact-driven progression"). Their semantic cores are preserved under the
new identities `PROTO-GO-INV-063` and `PROTO-GO-INV-064`.

## Consequences

The following consequences are accepted:

1. proto-go no longer owns generic execution continuity, control transfer,
   execution occurrences, session re-entry, or child-workflow call/return as
   Product Intent.

2. proto-go owns its implementation-to-publication workflow, domain state,
   authority, progression decisions, and completion conditions.

3. The proto-go workflow may request mechanical execution, main-agent
   continuation, or a child workflow call; proto-runtime realizes the requested
   execution form.

4. proto-go business truth is not changed by transient execution or session
   loss, and proto-go no longer specifies the generic mechanism by which a
   later execution context finds or continues an active workflow execution.

5. Distinct proto-go operations may still progress concurrently without global
   serialization, and managed authoring isolation remains required.

6. Actionable conditions preserve the same proto-go objective while authorized
   progress remains possible.

7. `PUBLISHED` remains authoritative historical truth, and normal successful
   completion still requires all applicable proto-go-owned closure obligations.

8. proto-go may delegate routine Git versioning to proto-ruu within a supplied
   WorkBoundary without absorbing proto-ruu semantics or surrendering
   publication ownership.

9. ADR-016's local procedural evolution requirement remains, now applying to
   the business workflow while generic execution plumbing is supplied below the
   workflow boundary.

## Non-decisions

This ADR does not decide:

* the proto-runtime API or implementation;
* the proto-go workflow language or artifact syntax;
* the proto-go workflow source layout;
* runtime persistence or state serialization;
* the child-call protocol;
* the WorkBoundary schema or representation;
* proto-ruu call granularity;
* main-agent harness integration;
* execution status vocabulary;
* scheduling, locking, leasing, or fencing implementation;
* process topology;
* programming language;
* the concrete representation of proto-go's business workflow
  responsibilities.

Those remain downstream derivation or architecture work.
