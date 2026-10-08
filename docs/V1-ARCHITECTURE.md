# V1 Architecture

Technical foundation. Scope: [V1-SCOPE.md](V1-SCOPE.md). Entities: [DOMAIN-MODEL.md](DOMAIN-MODEL.md). Layout: [PROJECT-STRUCTURE.md](PROJECT-STRUCTURE.md). ADRs: [adr/](adr/).

## Goal

A desktop-first, local-first automation IDE that can grow beyond Playwright Web UI **without** coupling the Canonical Test Model or domain to Playwright or to the React UI.

## Principles

| Principle | V1 meaning |
| --- | --- |
| Domain independence | Canonical Test Model, objects, Project, Execution do not import Playwright |
| Adapter-based infrastructure | Playwright is the V1 **Playwright Adapter** under a **Browser Adapter** |
| Local-first | Create, record, execute, report, objects — all local; no mandatory server |
| Modular boundaries | UI, application services, domain, engines, adapters stay separate |
| Extension points | API / mobile / desktop / cloud / AI adapters later; not implemented in V1 |

```text
Canonical Test Model
        │
 Execution Engine
        │
 Browser Adapter
        │
 Playwright Adapter     ← V1 only
        │
     Browser
```

## High-level structure

```text
┌─────────────────────────────────────┐
│  Desktop UI  (React + TypeScript)   │
└─────────────────┬───────────────────┘
                  │ Electron IPC
┌─────────────────▼───────────────────┐
│  Application Core                   │
│  Project · Application · Environment│
│  Browser Profile · Test · Suite     │
│  Objects · Test Data · Execution    │
│  Reporting                          │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│  Domain Core                        │
│  Canonical Test Model + entities    │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│  Engine Layer                       │
│  Recorder · Keyword Engine          │
│  Executor · Wait Engine             │
│  Object Resolver · Healing Engine   │
│  Evidence                           │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│  Adapter Layer                      │
│  Playwright Adapter · FS · SQLite   │
│  Git adapter: Future                │
└─────────────────────────────────────┘
```

Field-level entity definitions: [DOMAIN-MODEL.md](DOMAIN-MODEL.md).

## Electron / React / Node

| Process | Owns | Must not |
| --- | --- | --- |
| **Main** (Node) | Lifecycle, windows, IPC handlers, filesystem, SQLite, Playwright runtime, privileged ops, logging, child processes | Leak unrestricted FS/process APIs to the UI |
| **Renderer** (React) | Views, forms, editors, navigation, designer, results | Access filesystem, SQLite, or Playwright directly |

Typed IPC is the only renderer ↔ privileged-runtime boundary.

```text
Renderer → IPC command → Main → Application service → Domain / adapters
```

Validate IPC with typed request/response models. See [ADR-001](adr/ADR-001-desktop-architecture.md).

## Execution pipeline

```text
Test Step → preconditions → Wait/Retry → Object Resolver
    → primary locator
    → on miss: Healing Engine → candidate + confidence
    → Action Executor → Assertion → Evidence → Step Result
```

### Healing Engine placement

Self-healing is **inside object resolution**, not a separate product mode.

V1 order: (1) primary locator (2) alternative stable locator (3) attribute/role/text (4) DOM similarity (5) confidence (6) execute only if threshold met (7) record healing Evidence.

Low-confidence healing must fail explicitly. AI/visual strategies: **Future**.

## Canonical Test Model

Recorder, Manual, and Script **converge** on one Playwright-independent model, then execute:

```text
Recorder ──┐
Manual ────┼──► Canonical Test Model ──► Execution Engine
Script ────┘
```

Intent, not Playwright code, is the source of truth (example: `{ "action": "click", "target": { "objectId": "loginButton" } }`). See [ADR-004](adr/ADR-004-test-model.md).

## Local-first and future cloud

**V1:** Desktop → local runtime → Playwright Adapter → browser. No required backend. [ADR-003](adr/ADR-003-local-first.md).

**Future:** same Canonical Test Model through an **execution interface** to local *or* remote runtime. Do not change the Test Model to add cloud.

```text
Desktop → Execution Interface → Local Runtime
                              → Remote Runtime   (Future)
```

## Extensibility (architectural, not infra scale)

Stable module boundaries, domain services, shared contracts, pluggable adapters, testable engines. Infra scale (workers, cloud DB) later **without** redesigning the Canonical Test Model.

**Reliability:** fail explicitly · validate inputs · never silently heal low-confidence objects · Evidence for failures and healing · structured Execution statuses · immutable completed history · credentials separate from tests · deterministic behavior before AI.
