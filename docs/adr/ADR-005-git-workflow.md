# ADR-005: Lightweight Git Workflow

## Status

Accepted

## Context

The product repo has one developer (soon possibly two). History and review matter; GitFlow does not.

## Decision

Trunk `main` plus short-lived `feature/*`, `fix/*`, `refactor/*`, `docs/*`, `chore/*`. Merge via pull request when practical. Conventional Commits. Protect `main` with CI; require review when a second developer joins.

Procedure: [GIT-WORKFLOW.md](../GIT-WORKFLOW.md).

This ADR does not cover user Project version control beyond “files must be Git-friendly.”

## Alternatives Considered

| Option | Outcome |
| --- | --- |
| GitFlow (`develop`/`release`) | Excess branches for 1–2 people. Deferred. |
| Commit straight to `main` | Weak review/CI gate. Rejected as the default. |

## Consequences

**Positive:** simple onboarding; readable history; low overhead; `main` stays the integration branch.

**Negative:** no release-train branches until packaging/process needs them.
