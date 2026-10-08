# ADR-004: Canonical Test Model

## Status

Accepted

## Context

Tests are created via Recorder, Manual/keyword, and Script. Three independent representations would fork the product.

## Decision

One **Canonical Test Model** is the source of test intent. A Test Case contains ordered Test Steps (`stepId`, `action`, `target` by `objectId`, plus assertion/data/control as applicable). All authoring modes (Recorder, Manual/Keyword, Script) converge on it; the Execution Engine consumes it.

```text
Recorder ──┐
Manual ────┼──► Canonical Test Model ──► Execution
Script ────┘
```

It is not a list of Playwright commands.

```json
{ "action": "click", "target": { "objectId": "loginButton" } }
```

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| Playwright scripts as source of truth | Couples tests to one engine; Recorder/Manual diverge. Rejected. |
| Separate models per authoring mode | Duplicate execution and healing paths. Rejected. |

## Consequences

**Positive:** one execution path; script generation possible; Future AI can emit the same model; engines remain replaceable.

**Negative:** the model must be designed and versioned; Script ↔ model sync is extra work.
