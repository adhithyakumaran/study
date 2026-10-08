# Tech Stack

V1 technology choices. Architecture: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md). Decisions: [ADR-001](adr/ADR-001-desktop-architecture.md), [ADR-002](adr/ADR-002-playwright.md), [ADR-003](adr/ADR-003-local-first.md).

## V1 stack

| Layer | Technology | Purpose | Reason |
| --- | --- | --- | --- |
| Desktop shell | Electron | App window, main process, packaging host | Node + Chromium; local Playwright; IDE-like app |
| UI | React | Renderer views and editors | Component model fits a dense desktop IDE |
| Language | TypeScript | UI, main, domain, scripts, contracts | One language and shared types |
| Runtime | Node.js | Main process and engines | Native to Electron and Playwright |
| Browser automation | Playwright | V1 Playwright Adapter; also product E2E tests | Modern browsers, contexts, wait, trace, screenshots |
| Script editor | Monaco | In-app TypeScript editing | VS Code-grade editor in Electron |
| Local DB | SQLite | App metadata, indexes, Execution history, caches | Zero-server, transactional, portable |
| DB access | Drizzle | Typed SQLite schema/access | Lightweight, TypeScript-native |
| Validation | Zod | Runtime checks at IPC/config/domain edges | Aligns compile-time types with runtime |
| UI state | Zustand | Renderer state | Small, no extra UI framework |
| Unit tests | Vitest | Domain/engine/service tests | Fast, TypeScript-native |
| Lint | ESLint | Static checks | Standard TS/React linting |
| Format | Prettier | Formatting | One style |
| Packaging | electron-builder | Installers | Established Electron packaging |
| Source control | Git + GitHub | Product repo collaboration | PR/CI workflow in [GIT-WORKFLOW.md](GIT-WORKFLOW.md) |

Do not add stack items in V1 unless a new ADR says so.

## Why TypeScript

One language across React, Electron, Node.js, Playwright Adapter, domain, Canonical Test Model, user scripts, and IPC contracts. Shared types reduce drift at process and package boundaries.

## Why Playwright

V1 needs local Web UI automation: Chromium/Chrome/Edge/Firefox, browser contexts, auto-waiting, screenshots, tracing, locators, TypeScript.

Playwright is **not** the domain model. The Execution Engine talks to a **Browser Adapter**; V1 implements **Playwright Adapter** only. [ADR-002](adr/ADR-002-playwright.md).

## Why Electron

Local filesystem, SQLite, Playwright, and an IDE UI require a desktop runtime. Electron: Chromium renderer, Node main, TypeScript, Monaco, packaging.

Tauri is **not** V1 (Rust + Node/Playwright split). Reconsider only if footprint becomes a proven problem. [ADR-001](adr/ADR-001-desktop-architecture.md).

## Why SQLite / Project vs runtime state

No DB server. Split storage:

| Kind | Storage |
| --- | --- |
| User-owned Project artifacts | Portable, Git-friendly files (source of truth) |
| Application / runtime state | SQLite under `.internal/` |

**Project source of truth:** `project.json`, `applications/`, `environments/`, `browsers/`, `objects/`, `tests/`, `suites/`, `data/`, `scripts/`.

**SQLite may contain:** recent-project indexes, execution history/indexes, search indexes, caches, internal application state, other non-authoritative runtime metadata.

**Rule:** SQLite MUST NOT become the only or source-of-truth representation of user-owned tests, objects, configuration, suites, or scripts. [ADR-003](adr/ADR-003-local-first.md).

## Local infrastructure model

Required for V1: user machine · desktop app · Playwright · browser · local files + SQLite.

No mandatory application server.

**Security baseline (V1):** credential *references* only, never secrets in Test Cases · no secrets in Git · validate IPC and imported Projects · restrict FS to approved Project paths · do not expose unrestricted child-process APIs to the renderer.

## Future technology extensions

Behind stable interfaces, not V1: remote workers, cloud control plane, object storage, central DB, CI adapters, LLM providers, API/mobile adapters, enterprise identity/RBAC.
