# proto-go

`proto-go` is the managed end-to-end implementation-to-publication procedure of the
coding-agent Development System.

The user-facing invocation of proto-go is `/go`.

proto-go is executed as a main-agent-orchestrated skill procedure. The `/go`
skill governs the main agent. A `/go` invocation enters Invocation Preflight,
which may itself contain terminating proto-go script invocations,
Continuation Artifacts, and authorized main-agent continuations before an
admission-complete Launch Contract exists. Admission begins the logical
proto-go operation; its first mechanical transition is then a fresh terminating
proto-go script invocation. Every script invocation terminates before the
corresponding main-agent continuation occurs.

The repository is currently in the product-definition phase.

Its authoritative starting point is:

- [`docs/specification/proto-go-spec.md`](docs/specification/proto-go-spec.md) — normative
  product meaning;
- [`docs/adr/`](docs/adr/) — accepted decision history once product decisions
  are recorded;
- [`docs/vision/proto-go-vision.md`](docs/vision/proto-go-vision.md) — non-normative
  motivation and direction;
- [`docs/repository-governance/`](docs/repository-governance/) — repository
  procedure and engineering governance.

No implementation architecture, programming language, runtime, persistence
mechanism, registry format, version-control adapter, or formal model is
established merely by this repository layout.

The current Product Intent does select managed Git authoring isolation:
proto-go automatically provisions dedicated temporary detached Git worktrees
for managed authoring. Lower-level worktree topology and representation, such
as filesystem path, naming, registry, base-commit selection, cleanup
implementation, and reconstruction, remain undecided.

The current Product Intent does establish that `READY FOR HANDOFF` is an
internal durable lifecycle boundary and that publication is mandatory for
normal successful completion of `proto-go`. Normal successful completion
additionally requires every applicable proto-go-owned closure obligation,
including automatic temporary managed-worktree cleanup, to be satisfied.
The current `proto-go` must satisfy that contract.

ADR-002 further establishes that one logical `proto-go` operation owns one
`ManagedContribution`, and that a `ManagedContribution` may span one or more
repositories without repository boundaries defining its identity.

The normative invariant set is recorded in
[`docs/specification/proto-go-spec.md`](docs/specification/proto-go-spec.md).

The currently admitted invariant identifiers are `PROTO-GO-INV-001` through
`PROTO-GO-INV-058`.

`PROTO-GO-INV-008` is superseded by ADR-013.

`PROTO-GO-INV-012` is superseded by ADR-006.

`PROTO-GO-INV-020` is superseded by ADR-014.

`PROTO-GO-INV-021` is superseded by ADR-014.

`PROTO-GO-INV-022` is superseded by ADR-014.

`PROTO-GO-INV-024` is superseded by ADR-008.

`PROTO-GO-INV-030` is superseded by ADR-009.

`PROTO-GO-INV-032` is superseded by ADR-009.

`PROTO-GO-INV-041` is superseded by ADR-013.

ADR-003 establishes that `proto-go` does not own validation policy:
`READY FOR HANDOFF` requires every applicable governing validation obligation to
be satisfied, and readiness fails closed when `proto-go` cannot establish the
applicable obligation set with sufficient authority.

ADR-004 establishes that repository participation may expand during authoring
without creating a new `ManagedContribution`, provided each newly participating
repository enters managed authoring authority before its first managed mutation.

A `READY FOR HANDOFF` occurrence applies to a determinate authored state and
repository participation; later authored mutation requires a new applicable
readiness occurrence.

ADR-005 established that the effects bound by one readiness occurrence form one
indivisible logical publication unit. ADR-014 supersedes that all-or-none
cross-repository publication-visibility semantics in favor of aggregate
publication completion.

ADR-007 establishes that publication authority from a readiness occurrence must
be retired before authored mutation resumes. Historical readiness remains
valid. ADR-015 narrows the fencing to the ManagedContribution-level publication
boundary and to new publication initiation: already-originated repository-local
publication may complete after retirement.

`/go` first enters Invocation Preflight.

An admission-complete machine-readable Launch Contract must exist before a
logical proto-go operation is admitted. Its completeness may be established
through pre-Admission artifact-driven progression involving terminating script
invocations and authorized main-agent continuations.

After Admission, mechanical progression occurs through terminating proto-go
script invocations that emit machine-readable Continuation Artifacts.

The `/go` skill owns Continuation Policy; the main agent executes authorized
continuations and may later invoke the script again as a fresh invocation.

ADR-009 extends artifact-driven progression across Admission and publication.
Pre-Admission progression may invoke the terminating proto-go script before the
Launch Contract is complete, and may establish that Launch Contract through
script invocations and authorized continuations. `/go` may re-enter an
outstanding continuation without creating a new logical objective. `READY FOR
HANDOFF` remains intermediate; `PUBLISHED` remains required for normal
successful completion. Every main-agent/script control transfer requires sufficient
machine-readable authoritative Progression Context, so correctness does not
depend on conversational memory.

ADR-010 requires managed authored mutation to occur in automatically
provisioned dedicated temporary detached Git worktrees bound to one
`ManagedContribution` and participating repository. The invoking checkout and
other contributions' worktrees are not managed authoring surfaces.

ADR-011 makes progression session-agnostic: `/go` may originate from any
eligible main-agent session, a progression may be continued sequentially from
another session, distinct progressions may advance concurrently, and at most
one independent main-agent controller may advance one progression at a time.

ADR-012 makes `/go` procedural instructions loadable incrementally from a
bounded bootstrap; unrelated instruction material is not required upfront.

ADR-013 generalizes artifact-driven continuation to actionable mechanical
problems and proto-go-owned closure obligations. `PUBLISHED` is an
authoritative historical fact that a later cleanup failure must not revert, and
normal successful completion requires both publication and satisfaction of all
applicable closure obligations. Automatic managed-worktree cleanup is one such
closure obligation.

ADR-014 makes multi-repository publication an aggregate completion condition:
one readiness occurrence defines a complete set of independently satisfiable
Repository Publication Obligations, and the `ManagedContribution` establishes
`PUBLISHED` only when an effective readiness occurrence exists and all of its
publication obligations are satisfied. Cross-repository atomic publication
visibility is not required.

ADR-015 allows repository-local publication operations already originated before
readiness retirement to complete afterward. Retirement prevents the retired
readiness occurrence from establishing `PUBLISHED` and from initiating new
publication work, but does not require cancellation or fencing of
already-originated repository-local publication.

Implementation must be derived from accepted product semantics rather than
retroactively defining them.
