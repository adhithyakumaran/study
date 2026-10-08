# ADR-001: Desktop Architecture

## Status

Accepted

## Context

V1 needs a Katalon-like IDE: local filesystem, SQLite, Playwright, and a dense UI, without a product backend.

## Decision

Use **Electron + React + TypeScript**.

- React: renderer UI only.
- Electron main / Node.js: privileged operations (FS, SQLite, Playwright runtime, window lifecycle).
- Renderer talks to main through **typed IPC** only.

Details: [V1-ARCHITECTURE.md](../V1-ARCHITECTURE.md), [TECH-STACK.md](../TECH-STACK.md).

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| Tauri | Smaller footprint, but Rust plus a split Node/Playwright runtime. Not V1. |
| Browser-only web app | Cannot own local FS, Playwright, and IDE privileges without a backend. Rejected. |

## Consequences

**Positive:** one TypeScript stack; local Playwright; electron-builder; Monaco fits the renderer.

**Negative:** large Electron footprint; IPC must be locked down; main/renderer split is mandatory complexity.
