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
→ invokes downstream version-control progression
→ continues authored correction / convergence when the Development System can
  resolve a mechanical publication block
→ reaches successful completion only at the governing publication outcome

downstream version-control system
→ owns its specialized mechanical version-control progression
```

Today, `git-commits-push` may serve as a temporary downstream mechanical
publication mechanism.

Ruu may later serve as a more general agentic Git convergence and
version-control progression system.

Neither relationship defines `proto-go` Product Intent.

The current `proto-go` must satisfy its Product Intent without depending on
Turnlock, GCP, Ruu, or another future component gaining capabilities that do not
exist.

Future integrations must conform to `proto-go`; `proto-go` semantics must not be written
as promises that only become true after a future integration exists.

The initial repository deliberately remains specification-first.

The immediate work is to derive the product model, obligations, invariants, and
open semantic decisions from the accepted Product Intent before selecting the
implementation architecture.
