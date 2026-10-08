# Project Structure

Source repo and user Project layout. Git process: [GIT-WORKFLOW.md](GIT-WORKFLOW.md). Architecture: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md).

## Source repository

```text
qa-automation-studio/
├── apps/
│   └── desktop/
│       ├── main/                 # Electron main (privileged)
│       └── renderer/             # React UI only
├── packages/
│   ├── domain/                   # Entities and domain rules
│   ├── test-model/               # Canonical Test Model
│   ├── object-model/             # Test Object / Object Repository contracts
│   ├── execution-engine/         # Orchestration; Playwright-independent
│   ├── keyword-engine/
│   ├── recorder/                 # Browser events → Canonical Test Model
│   ├── healing-engine/           # Recovery + confidence
│   └── shared/                   # Truly shared types/utils only
├── tests/
├── docs/
│   └── adr/
├── scripts/
├── package.json
├── tsconfig.json
├── eslint.config.*
├── prettier.config.*
└── README.md
```

Package names are module boundaries, not a requirement to publish npm packages.

## Desktop main process

```text
apps/desktop/main/
├── ipc/
├── services/
├── storage/          # SQLite
├── playwright/       # Playwright Adapter / runtime
├── processes/
├── logging/
└── config/
```

Owns: Electron lifecycle, IPC, filesystem, SQLite, Playwright, process management, application services.

## Renderer

```text
apps/desktop/renderer/
├── pages/
├── components/
├── features/
├── hooks/
├── stores/
├── routes/
└── styles/
```

UI only. No filesystem, SQLite, or Playwright.

## Shared packages (responsibility)

| Package | Contains |
| --- | --- |
| `domain` | Entities and rules |
| `test-model` | Test Case with nested Test Steps, variables, Assertion |
| `object-model` | Test Objects, locators, Object Repository |
| `execution-engine` | Run orchestration (no Playwright types) |
| `keyword-engine` | Keyword metadata and execution contracts |
| `recorder` | Capture → Canonical Test Model |
| `healing-engine` | Strategies + confidence |
| `shared` | Cross-cutting contracts only; keep small |

## User Project (on disk)

User-owned Project state is stored as portable, Git-friendly project files. Application-owned runtime state is stored in SQLite under `.internal/`.

**Project source of truth:** `project.json`, `applications/`, `environments/`, `browsers/`, `objects/`, `tests/`, `suites/`, `data/`, `scripts/`.

**SQLite may contain:** recent-project indexes, execution history/indexes, search indexes, caches, internal application state, other non-authoritative runtime metadata.

**Rule:** SQLite MUST NOT become the only or source-of-truth representation of user-owned tests, objects, configuration, suites, or scripts.

`tests/` holds Test Case artifacts (each Test Case contains ordered Test Steps). Do not require separate Test Step files.

```text
<project>/
├── project.json            # SoT
├── applications/           # SoT
├── environments/           # SoT
├── browsers/               # SoT (Browser Profiles)
├── objects/                # SoT (Object Repository)
├── tests/                  # SoT (Test Cases / Canonical Test Model)
├── suites/                 # SoT
├── data/                   # SoT
├── scripts/                # SoT
├── executions/             # generated run output (not SoT)
├── reports/                # generated (not SoT)
└── .internal/
    └── project.db          # SQLite runtime/internal state (not SoT)
```

Exact filenames may evolve. Generated `executions/` / `reports/` may exist on disk; indexes/history may also live in SQLite. Neither replaces the source-of-truth directories above.

## Dependency direction

```text
UI → Application services → Domain → Ports/interfaces → Infrastructure adapters
```

Forbidden:

```text
UI → SQLite
UI → Playwright
Domain → Electron
Domain → filesystem
```

## Rules

- Domain / Canonical Test Model packages must not import Electron or Playwright.
- Renderer must not use Node filesystem APIs.
- Infrastructure implements domain/application ports (Browser Adapter, Playwright Adapter, FS, SQLite).
- `shared` stays small.

**Naming:** components/types `PascalCase` · functions/variables `camelCase` · one file-name convention, enforced · IDs are stable generated values, never UI labels.
