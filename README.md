# proto-go

`proto-go` is the managed end-to-end implementation-to-publication procedure of the
coding-agent Development System.

The user-facing invocation of proto-go is `/go`.

proto-go is an implementation-to-publication workflow that owns its domain
semantics and progression decisions. Its generic execution continuity and
control-transfer mechanics are provided by proto-runtime. A `/go` invocation
begins or continues a proto-go workflow execution; it may enter Invocation
Preflight before an admission-complete Launch Contract exists, and Admission
begins the logical proto-go operation. Routine Git versioning progression is
delegated to proto-ruu.

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

No implementation architecture, programming language, runtime API, persistence
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
`PROTO-GO-INV-065`.

Superseded identities are historical only:

```text
ADR-013: PROTO-GO-INV-008, PROTO-GO-INV-041
ADR-006: PROTO-GO-INV-012
ADR-014: PROTO-GO-INV-020 through PROTO-GO-INV-022
ADR-008: PROTO-GO-INV-024
ADR-009: PROTO-GO-INV-030, PROTO-GO-INV-032
ADR-017: PROTO-GO-INV-023, PROTO-GO-INV-025,
         PROTO-GO-INV-033 through PROTO-GO-INV-038,
         PROTO-GO-INV-042, PROTO-GO-INV-046,
         PROTO-GO-INV-048 through PROTO-GO-INV-050,
         PROTO-GO-INV-052
```

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
through pre-Admission proto-go progression, which may use execution realized by
proto-runtime.

After Admission, the proto-go workflow owns continued business progression.
proto-go may request mechanical execution, a main-agent continuation, or a
child workflow call; proto-runtime realizes the requested execution form and
returns execution truth.

ADR-009 established state/authority-driven progression across Admission and
publication. `READY FOR HANDOFF` remains intermediate and `PUBLISHED` remains
required for normal successful completion. ADR-017 supersedes the
script/artifact runtime mechanics while preserving those semantics.

ADR-010 requires managed authored mutation to occur in automatically
provisioned dedicated temporary detached Git worktrees bound to one
`ManagedContribution` and participating repository. The invoking checkout and
other contributions' worktrees are not managed authoring surfaces.

ADR-011 established concurrent, session-agnostic progression. ADR-017
preserves the user-visible domain consequences — distinct proto-go operations
may progress concurrently and proto-go business identity is not owned by a
conversational session — while generic controller coordination, session
transfer, and execution re-entry are provided by proto-runtime.

ADR-012 required `/go` procedural instructions to be loadable incrementally
from a bounded bootstrap. ADR-017 supersedes that requirement as proto-go
Product Intent; instruction loading and execution continuity belong to
proto-runtime and the surrounding harness.

ADR-013 generalized continuation to actionable mechanical problems and
proto-go-owned closure obligations. `PUBLISHED` is an authoritative historical
fact that a later cleanup failure must not revert, and normal successful
completion requires both publication and satisfaction of all applicable
closure obligations. ADR-017 preserves those semantics while superseding the
artifact/script continuation mechanism. Automatic managed-worktree cleanup is
one such closure obligation.

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

ADR-016 requires proto-go's implementation-to-publication procedure to remain
locally evolvable and explicitly composable: a procedural change whose semantic
effect is local must require changes proportional to that effect rather than
unrelated changes across the rest of the procedure caused by orchestration
plumbing, and the effective procedure must remain explicitly identifiable as a
composition of independently understandable procedural responsibilities. It
does not select a concrete composition representation and does not require
proto-go to become a general-purpose workflow runtime or a Turnlock substitute.

ADR-017 delegates generic workflow execution to proto-runtime. proto-go is the
implementation-to-publication workflow and owns domain semantics and
progression decisions; proto-runtime provides generic execution continuity,
control transfer, execution occurrence truth, and child-workflow call/return.
proto-go no longer owns script, artifact, continuation-policy, or
progression-context machinery. proto-ruu is the specialized
routine-Git-versioning workflow used by proto-go within a supplied
`WorkBoundary`; proto-go retains ownership of readiness, Repository Publication
Obligations, aggregate `PUBLISHED` semantics, and normal successful completion.

Implementation must be derived from accepted product semantics rather than
retroactively defining them.
