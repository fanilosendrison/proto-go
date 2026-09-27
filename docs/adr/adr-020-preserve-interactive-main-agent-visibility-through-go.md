# ADR-020: Preserve interactive main-agent visibility through /go

- Status: Accepted
- Date: 2026-09-27

## Context

`/go` is the user-facing entry point of proto-go. ADR-017 established that
proto-go owns its domain workflow while Prelock provides generic execution
continuity and control-transfer mechanics. A proto-go progression may therefore
cross execution contexts even while the user-facing objective remains the same.

Outside proto-go, an interactive coding harness ordinarily exposes user-visible
main-agent activity while the main agent works: user-facing messages,
progress/status communication, tool invocation surfaces, tool-result surfaces,
and other activity that the harness normally makes visible.

The product owner has resolved that invoking `/go` must not turn later
proto-go-requested main-agent work into an opaque background operation merely
because control, execution, or compute crosses internal boundaries.

This requirement concerns the user-facing continuity of main-agent activity. It
does not transfer workflow orchestration authority to the main agent and does
not require private or infrastructure-internal state to become visible.

## Decision

proto-go establishes **Interactive Main-Agent Visibility** as Product Intent.

Interactive Main-Agent Visibility is the product guarantee that, while an
eligible user-facing interactive coding-harness context is currently carrying a
proto-go progression, main-agent work requested by that progression remains
observable through that interaction with the ordinary class of user-visible
main-agent activity that the harness exposes for equivalent main-agent work
outside proto-go.

The guarantee includes, only to the extent ordinarily exposed by the active
harness:

- main-agent user-facing messages;
- main-agent progress/status communication;
- tool invocation surfaces;
- tool-result surfaces; and
- other ordinary user-visible main-agent activity.

An internal execution-boundary change MUST NOT, by itself, downgrade such
main-agent work into an opaque background operation.

Conceptually:

```text
interactive user-facing harness context
        ↓
user invokes /go
        ↓
proto-go owns workflow progression
        ↓
execution/control boundaries may change
        ↓
proto-go requests main-agent work
        ↓
ordinary user-visible main-agent activity remains observable
through the current eligible interactive harness context
```

Control ownership and visibility are distinct:

```text
user-visible interaction
!=
workflow orchestration authority
```

Preserving Interactive Main-Agent Visibility does not make the main agent the
global workflow orchestrator. proto-go retains its existing authority over
domain semantics and progression decisions, and ADR-017's Prelock boundary is
unchanged.

The guarantee also does not require exposure of information that is not
ordinarily user-visible, including private chain-of-thought, hidden model state,
system/developer prompts, credentials, secrets, private runtime state, Prelock
internal state transitions, workflow persistence internals, infrastructure
control state, low-level environment-provisioning logs, daemon/system logs, or
internal telemetry.

The user MUST NOT be required to leave the invoking coding-harness experience
and use an infrastructure-specific or proto-go-specific monitoring interface
merely to retain ordinary visibility into current main-agent work. Diagnostic,
administrative, recovery, infrastructure, and debugging interfaces may still
exist for their respective purposes.

Interactive Main-Agent Visibility does not require the originating
conversational session to survive. If the original interactive context remains
the current eligible user-facing context, main-agent activity remains visible
there. If it disappears, proto-go workflow continuity remains governed by the
existing session-independent rules. When a later eligible interactive context
re-enters and carries the user-facing continuation, subsequent main-agent work
is subject to the same Interactive Main-Agent Visibility guarantee in that
context.

The guarantee does not require preservation of one main-agent process, model
process, execution occurrence, or cognitive lineage.

This decision governs proto-go-requested main-agent work. It does not require
every mechanical execution, child-workflow internal event, proto-ruu transition,
Prelock event, workflow-state transition, or environment-management operation
to become a user-visible stream.

No transport or streaming mechanism is selected.

This decision does not modify Prelock Product Intent or assign a new generic
runtime responsibility to Prelock. ADR-017 remains controlling for the
workflow/execution boundary.

This decision establishes Product Intent for later explicit invariant and
architecture derivation. It does not introduce a new `PROTO-GO-INV-*` identity.

## Consequences

* `/go` preserves the ordinary interactive visibility of main-agent work rather
  than turning that work opaque because execution moved internally.
* The user-facing activity surface remains conceptually separate from workflow
  control ownership.
* Execution context, process, machine, or substrate changes may occur without
  changing this user-facing guarantee.
* The user does not need an infrastructure console or separate monitoring UI
  merely to observe ordinary main-agent activity.
* Session loss remains compatible with proto-go continuity; visibility follows
  whichever eligible interactive context currently carries the user-facing
  continuation.
* Private reasoning, secrets, low-level runtime state, and infrastructure
  internals do not become user-visible under this decision.
* A later explicit derivation must determine the invariants and architecture
  needed to satisfy this Product Intent.

## Non-decisions

This ADR does not decide:

* a streaming protocol;
* WebSocket, SSE, PTY, remote PTY, SSH, terminal multiplexing, event bus,
  message broker, RPC streaming, webhook, or polling mechanisms;
* Pi-specific, Codex-specific, or other harness-specific transport;
* a Prelock API or new Prelock capability;
* process, machine, VM, container, or network topology;
* persistence of the user-visible activity stream;
* replay/history semantics for activity already displayed;
* buffering, ordering, backpressure, reconnect, or delivery protocol;
* a requirement to expose private chain-of-thought or hidden model state;
* visibility of all mechanical/child-workflow/internal runtime activity;
* a new invariant identifier;
* the architecture required to implement this Product Intent.
