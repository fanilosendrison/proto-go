# proto-go Vision

> Non-normative direction. This document does not override the Product Intent,
> accepted ADRs, or future derived invariants.

`proto-go` is intended to become the managed implementation-production layer of an
agentic software-development system.

Its long-term role is to let the Development System transform an implementation
intent into an isolated, managed, validated contribution that can be handed to a
version-control progression system without requiring the user to coordinate the
underlying authoring environment manually.

The intended conceptual composition is:

```text
Development System / user
→ determines what should be implemented

proto-go
→ manages implementation production
→ establishes required validation
→ establishes durable readiness
→ requests downstream version-control progression
→ continues authored correction / convergence when the Development System can
  resolve a publication block
→ reaches successful completion only at the governing publication outcome

Prelock
→ provides generic execution continuity for the proto-go workflow

proto-ruu
→ owns routine Git versioning progression within the supplied WorkBoundary

downstream version-control system
→ owns its specialized mechanical version-control progression
```

Prelock is the reusable execution substrate intended to execute proto-go's
workflow. proto-ruu is the specialized routine-Git-versioning workflow intended
to be used by proto-go for version-control progression.

Today, `git-commits-push` may serve as a temporary downstream mechanical
publication mechanism.

Ruu may later serve as a more general agentic Git convergence and
version-control progression system.

None of these relationships defines `proto-go` Product Intent.

The current product shape separates proto-go's implementation-to-publication
workflow semantics from generic execution. proto-go owns its domain progression
decisions; Prelock provides execution continuity and control transfer,
including mechanical execution, main-agent continuation, and child-workflow
call/return. proto-go delegates routine Git versioning progression to proto-ruu
within a supplied `WorkBoundary`.

Future integrations must conform to `proto-go`; `proto-go` semantics must not be written
as promises that only become true after a future integration exists.

The initial repository deliberately remains specification-first.

The immediate work is to derive the product model, obligations, invariants, and
open semantic decisions from the accepted Product Intent before selecting the
implementation architecture.
