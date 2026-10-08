# Git Workflow

Product **source** repository only. User Project Git-friendliness: [TECH-STACK.md](TECH-STACK.md), [PROJECT-STRUCTURE.md](PROJECT-STRUCTURE.md). Decision: [ADR-005](adr/ADR-005-git-workflow.md). Architecture is not defined here.

## Branches

GitHub. Trunk: `main` (always buildable). Team size 1–2: lightweight branches. **Not** GitFlow. No permanent `develop` / `release` / `staging` / `production` unless release process later requires them.

```text
main
 ├── feature/*
 ├── fix/*
 ├── refactor/*
 ├── docs/*
 └── chore/*
```

| Prefix | Use |
| --- | --- |
| `feature/` | New capability |
| `fix/` | Bug |
| `refactor/` | Structure change, same behavior |
| `docs/` | Documentation |
| `chore/` | Tooling, deps, config |

Names: short, kebab-case (`feature/project-creation`, `fix/recent-project-loading`). No feature work on `main`.

## Commits

[Conventional Commits](https://www.conventionalcommits.org/): `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`. Focused diffs. Reject: `update`, `final changes`, `stuff`, `done`.

```text
feat: add project creation flow
fix: validate project path
docs: add V1 architecture
```

## PR flow

```text
branch from main → implement → test → push → PR → review → CI → merge to main
```

**PR body:** what · why · how tested · UI screenshots · known limits · related issue.

**Review:** architecture boundaries · Canonical Test Model (no Playwright leak) · no renderer FS/SQLite/Playwright · security · errors · tests · naming · duplication.

## CI expectations

CI on PRs must fail the merge if any of these fail: TypeScript compile · ESLint · Prettier check · unit tests (Vitest). Add integration/E2E jobs when those suites exist. Do not merge red CI.

## `main` protection

When GitHub rules are enabled: PR required · CI green required · no force-push · require a reviewer once a second developer joins. Keep rules simple.

## Versioning

SemVer `MAJOR.MINOR.PATCH` when packaging starts (`0.1.0` … `1.0.0`). Do not label a **product** V1 release until [V1-SCOPE.md](V1-SCOPE.md) success criteria are met.

## `.gitignore` principles

Ignore generated/machine files, not user-owned Project artifacts.

**Ignore:** `node_modules/`, `dist/`, `build/`, `.env`, `*.log`, `.internal/cache/`, temporary browser data, OS junk.

**Do not ignore:** tests, objects, project/application/environment/browser config, scripts the user authored.
