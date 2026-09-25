# proto-go ADR Index

| ADR | Decision | Status |
| --- | -------- | ------ |
| [ADR-001](adr-001-make-publication-the-successful-terminal-outcome-of-proto-go.md) | Make publication the successful terminal outcome of `proto-go` | Accepted |
| [ADR-002](adr-002-allow-one-managed-contribution-to-span-multiple-repositories.md) | Allow one ManagedContribution to span multiple repositories | Accepted |
| [ADR-003](adr-003-keep-validation-policy-outside-proto-go-and-fail-closed-on-unknown-obligations.md) | Keep validation policy outside `proto-go` and fail closed on unknown obligations | Accepted |
| [ADR-004](adr-004-allow-managed-contribution-repository-scope-to-expand-during-authoring.md) | Allow ManagedContribution repository scope to expand during authoring | Accepted |
| [ADR-005](adr-005-make-each-readiness-occurrence-an-indivisible-publication-unit.md) | Make each readiness occurrence an indivisible publication unit | Accepted |
| [ADR-006](adr-006-define-main-agent-skill-orchestration-and-terminating-script-execution.md) | Define main-agent skill orchestration and terminating script execution | Accepted |
| [ADR-007](adr-007-fence-prior-readiness-publication-authority-before-authored-resumption.md) | Fence prior readiness publication authority before authored resumption | Accepted |
| [ADR-008](adr-008-establish-launch-contract-and-artifact-driven-continuation.md) | Establish Launch Contract and artifact-driven continuation | Accepted |
| [ADR-009](adr-009-extend-artifact-driven-progression-across-admission-and-publication.md) | Extend artifact-driven progression across admission and publication | Accepted |
| [ADR-010](adr-010-require-temporary-detached-git-worktrees-for-managed-authoring.md) | Require temporary detached Git worktrees for managed authoring | Accepted |
| [ADR-011](adr-011-make-progression-session-agnostic-and-single-controller.md) | Make proto-go progression session-agnostic and single-controller | Accepted |
| [ADR-012](adr-012-load-go-procedural-instructions-incrementally.md) | Load /go procedural instructions incrementally | Accepted |
| [ADR-013](adr-013-continue-progression-through-actionable-problems-and-terminal-cleanup.md) | Continue progression through actionable problems and terminal cleanup | Accepted |
| [ADR-014](adr-014-make-multi-repository-publication-an-aggregate-completion-condition.md) | Make multi-repository publication an aggregate completion condition | Accepted |
| [ADR-015](adr-015-allow-originated-repository-publication-to-complete-after-readiness-retirement.md) | Allow originated repository publication to complete after readiness retirement | Accepted |
| [ADR-016](adr-016-require-local-procedural-evolution-and-explicit-composition.md) | Require local procedural evolution and explicit procedural composition | Accepted |
| [ADR-017](adr-017-delegate-generic-workflow-execution-to-proto-runtime.md) | Delegate generic workflow execution to proto-runtime | Accepted |
| [ADR-018](adr-018-recognize-proto-runtime-rename-to-prelock.md) | Recognize proto-runtime rename to Prelock | Accepted |

The current normative product meaning is projected in
[`../specification/proto-go-spec.md`](../specification/proto-go-spec.md).

Accepted ADRs record explicit product or architectural decisions.

The normative specification must remain synchronized with accepted semantic
decisions.

ADR-013 supersedes only the terminality portions of ADR-001 and ADR-009 and the
normative invariants `PROTO-GO-INV-008` and `PROTO-GO-INV-041`. The historical
ADR files remain unchanged.

ADR-014 supersedes ADR-005's all-or-none cross-repository publication-visibility
semantics and supersedes `PROTO-GO-INV-020` through `PROTO-GO-INV-022`.

ADR-015 narrows ADR-007's fencing semantics under the ADR-014 model: retirement
fences global publication-completion authority and new publication initiation,
but does not require already-originated repository-local publication to be
cancelled or prevented from completing.

`PROTO-GO-INV-026` through `PROTO-GO-INV-028` remain normative.

ADR-017 reassigns generic workflow execution to proto-runtime and rescopes
proto-go to its domain workflow. It supersedes the execution-model portions of
ADR-006, ADR-008, ADR-009, ADR-011, ADR-012, and ADR-013, and narrows ADR-016's
relationship to the execution substrate.

ADR-017 supersedes `PROTO-GO-INV-023`, `PROTO-GO-INV-025`, `PROTO-GO-INV-033`
through `PROTO-GO-INV-038`, `PROTO-GO-INV-042`, `PROTO-GO-INV-046`,
`PROTO-GO-INV-048` through `PROTO-GO-INV-050`, and `PROTO-GO-INV-052`. It
amends `PROTO-GO-INV-031`, `PROTO-GO-INV-039`, `PROTO-GO-INV-040`,
`PROTO-GO-INV-045`, and `PROTO-GO-INV-047` without changing their semantic
property, and introduces `PROTO-GO-INV-059` through `PROTO-GO-INV-065`.
