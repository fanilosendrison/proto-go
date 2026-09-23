# proto-go ADR Index

| ADR | Decision | Status |
| --- | --- | --- |
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
