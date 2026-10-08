# Development Guidelines

Engineering rules for V1. Architecture: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md). Git: [GIT-WORKFLOW.md](GIT-WORKFLOW.md). Scope: [V1-SCOPE.md](V1-SCOPE.md).

## Definition of Done

- [ ] Behavior complete and boundaries respected (see Architecture rules)
- [ ] Validation + error handling at the right layer
- [ ] Unit tests where logic lives; integration/E2E for critical journeys
- [ ] `tsc`, ESLint, Prettier clean
- [ ] Docs/ADR updated if a decision or contract changed
- [ ] PR reviewed, CI green, merged to `main`

## TypeScript

| Do | Don't |
| --- | --- |
| `strict: true` | `any` (if unavoidable, comment why) |
| Explicit types at IPC/domain/adapter edges | Implicit `any` across processes |
| Discriminated unions, typed results/errors | Swallowing errors |
| Zod at runtime boundaries | Trusting renderer or file input |

## Error handling and logging

Errors: explicit, actionable, logged, surfaced at the correct layer. No empty `catch`.

Logs: structured (`timestamp`, `level`, `component`, `operation`, `projectId`, `executionId`, `testCaseId`, `errorCode`, `message`). **Never** log passwords, tokens, secrets.

## Validation

Validate: IPC input · Project import · config files · user paths · Execution config · adapter responses.

## UI

Clear journey · hide engine internals · useful validation · loading and error states · no extra config gates.

## Architecture rules

| Forbidden | Required |
| --- | --- |
| React → Playwright / filesystem / SQLite | Typed Electron IPC only |
| Business logic in UI components | Services + domain packages |
| Domain → Electron | Domain independent of desktop |
| Canonical Test Model → Playwright | Browser Adapter / Playwright Adapter |

Healing Engine is part of Object Resolver / Execution, not a side product. Fail closed on low confidence. Heuristics/AI (Future): expose confidence, Evidence, never silently change user intent.

## Testing strategy

| Layer | Tool / target |
| --- | --- |
| Unit | Vitest: domain rules, services, validation, locator ranking, healing confidence, Test Model transforms |
| Integration | Persistence, service/repos, IPC contracts, Playwright Adapter |
| E2E | Create/open Project, author (Recorder / Manual / Script), execute, view result |

Do not optimize early. Avoid extra browser launches, repeated full DOM scans, chatty IPC, unbounded logs, loading full artifacts when metadata suffices.

## Security

- [ ] No secrets in Git or in Test Case / Test Step definitions (credential references only)
- [ ] Path + IPC validation; no unrestricted renderer process APIs
- [ ] Sanitize imported Projects
- [ ] Credentials kept off test artifacts

## Feature workflow

1. Journey ([USER-JOURNEYS.md](USER-JOURNEYS.md))
2. Entities ([DOMAIN-MODEL.md](DOMAIN-MODEL.md))
3. Existing interfaces
4. Persistence (Project files = SoT; SQLite = `.internal/` runtime only)
5. UI
6. Tests
7. Docs / ADR if the decision is durable

Feature behavior → journeys/scope. Cross-cutting decisions → ADR.

## Documentation

Keep docs short. Cross-link; do not copy architecture into every file. Mark **V1** / **Future** / **Out of scope**.

## PR checklist

- [ ] Tests, `tsc`, lint, format
- [ ] No secrets; no unnecessary dependencies
- [ ] Architecture boundaries respected
- [ ] Docs updated
- [ ] UI screenshots
- [ ] Error cases considered
