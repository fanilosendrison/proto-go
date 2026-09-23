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
mechanism, worktree mechanism, registry format, version-control adapter, or
formal model is established merely by this repository layout.

The current Product Intent does establish that `READY FOR HANDOFF` is an
internal durable lifecycle boundary and that the normal successful terminal
outcome of `proto-go` is publication. The current `proto-go` must satisfy that contract.

ADR-002 further establishes that one logical `proto-go` operation owns one
`ManagedContribution`, and that a `ManagedContribution` may span one or more
repositories without repository boundaries defining its identity.

The normative invariant set is recorded in
[`docs/specification/proto-go-spec.md`](docs/specification/proto-go-spec.md).

The currently admitted invariant identifiers are `PROTO-GO-INV-001` through
`PROTO-GO-INV-042`.

`PROTO-GO-INV-012` is superseded by ADR-006.

`PROTO-GO-INV-024` is superseded by ADR-008.

`PROTO-GO-INV-030` is superseded by ADR-009.

`PROTO-GO-INV-032` is superseded by ADR-009.

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

ADR-005 establishes that the effects bound by one readiness occurrence form one
indivisible logical publication unit. Progressive preparation is permitted, but
a proper subset must not independently reach its governing publication outcome.

ADR-007 establishes that publication authority from a readiness occurrence must
be retired and fenced before authored mutation resumes. Historical readiness
remains valid, but retired readiness cannot later reach publication.

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
HANDOFF` remains intermediate; `PUBLISHED` remains the normal successful
terminal outcome. Every main-agent/script control transfer requires sufficient
machine-readable authoritative Progression Context, so correctness does not
depend on conversational memory.

Implementation must be derived from accepted product semantics rather than
retroactively defining them.
