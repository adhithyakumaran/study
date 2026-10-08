# ADR-002: Playwright as V1 Browser Automation Engine

## Status

Accepted

## Context

V1 is Web UI automation on the user’s machine. The engine must support modern browsers, waiting, traces, and TypeScript — without becoming the product domain.

## Decision

Playwright is the V1 browser automation engine, implemented as the **Playwright Adapter** behind a **Browser Adapter**.

```text
Execution Engine → Browser Adapter → Playwright Adapter → browser
```

The Canonical Test Model and domain packages must not import Playwright APIs.

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| Selenium | Broader legacy ecosystem; weaker TS/context/trace fit for this stack. Not V1. |
| Cypress | Runner-centric; poorer fit as an embedded local IDE engine. Not V1. |
| Playwright as domain | Rejected: would block other engines and leak API into tests. |

## Consequences

**Positive:** strong local Web automation; traces/screenshots; future engines can implement Browser Adapter without changing the Canonical Test Model.

**Negative:** team must keep Playwright types inside the adapter; leaking locators/APIs into the Test Model is a regression.
