# ADR-003: Local-First V1

## Status

Accepted

## Context

V1 must work without operating a cloud control plane or remote execution fleet.

## Decision

Core V1 runs **locally**: desktop app, Git-friendly Project files, SQLite (metadata/history/cache), Playwright, browser, Execution, reports.

No mandatory application server. User-owned artifacts stay on disk; SQLite is not the Project source of truth.

Future remote runs go through an **execution interface**; the Canonical Test Model does not change.

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| Cloud-required V1 | Higher cost, blocks offline core journey. Rejected. |
| SQLite as only Project store | Not Git-friendly; poor portability. Rejected. |

## Consequences

**Positive:** no V1 backend cost; offline core path; faster local runs; simpler first delivery.

**Negative:** no in-app collaboration or remote Execution in V1; sync is manual/Git; Git-aware IDE flows are V1.5+.
