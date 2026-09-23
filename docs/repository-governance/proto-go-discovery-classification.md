# proto-go Discovery Classification

This document governs how discoveries made while working on `proto-go` are routed.

It creates no product semantics.

## Core rule

Implementation, testing, formalization, or architecture work may reveal a
problem.

That discovery is allowed to expose under-specification.

It is not allowed to resolve product under-specification silently.

## Classes

Every material discovery must be classified as exactly one of:

### `product-contradiction`

Existing authoritative product statements cannot all remain true together.

Required action:

```text
STOP semantic implementation
→ report the contradiction
→ require explicit product resolution
```

Do not select one side implicitly.

### `decision-required`

The current Product Intent admits multiple materially different product
semantics and no accepted authority selects one.

Required action:

```text
STOP the affected derivation
→ state the decision that is required
→ present the exact alternatives supported by current authority
→ wait for explicit product-owner resolution
```

Do not choose based on implementation convenience.

### `derived`

The result follows necessarily from already accepted product authority.

Required action:

```text
record the derivation
→ preserve provenance to its governing authority
```

Do not present a derived consequence as a new product decision.

### `architecture-only`

Product semantics are already sufficient and the remaining choice is an
implementation or architecture mechanism with no product-visible semantic
difference under the governing contract.

Required action:

```text
resolve at the architecture layer
```

Do not promote mechanism into Product Intent without necessity.

### `implementation-only`

The issue concerns realization of already-decided semantics and does not alter
the product contract.

Required action:

```text
resolve in implementation
```

## Prohibition

The coding agent must never convert:

```text
"the specification does not say"
```

into:

```text
"I selected the most reasonable behavior"
```

when the missing behavior changes product meaning.

Missing product authority is a discovery, not implementation permission.
