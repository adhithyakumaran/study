# ADR-003: Local-First V1

## Status

Accepted

## Context

V1 must work without operating a cloud control plane or remote execution fleet.

## Decision

Core V1 runs **locally**: desktop app, Playwright, browser, Execution. No mandatory application server.

User-owned Project state is stored as portable, Git-friendly project files. Application-owned runtime state is stored in SQLite under `.internal/`.

**Project source of truth:** `project.json`, `applications/`, `environments/`, `browsers/`, `objects/`, `tests/`, `suites/`, `data/`, `scripts/`.

**SQLite may contain:** recent-project indexes, execution history/indexes, search indexes, caches, internal application state, other non-authoritative runtime metadata.

**Rule:** SQLite MUST NOT become the only or source-of-truth representation of user-owned tests, objects, configuration, suites, or scripts.

Future remote runs go through an **execution interface**; the Canonical Test Model does not change.

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| Cloud-required V1 | Higher cost, blocks offline core journey. Rejected. |
| SQLite as only Project store | Not Git-friendly; poor portability. Rejected (SQLite is runtime/internal state only). |

## Consequences

**Positive:** no V1 backend cost; offline core path; faster local runs; simpler first delivery.

**Negative:** no in-app collaboration or remote Execution in V1; sync is manual/Git; Git-aware IDE flows are V1.5+.
