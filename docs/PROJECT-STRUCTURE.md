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
| `test-model` | Test Case, Test Step, variables, Assertion |
| `object-model` | Test Objects, locators, Object Repository |
| `execution-engine` | Run orchestration (no Playwright types) |
| `keyword-engine` | Keyword metadata and execution contracts |
| `recorder` | Capture → Canonical Test Model |
| `healing-engine` | Strategies + confidence |
| `shared` | Cross-cutting contracts only; keep small |

## User Project (on disk)

Git-friendly artifacts. SQLite under `.internal/` is local metadata, not the Project source of truth.

```text
<project>/
├── project.json
├── applications/         # e.g. endless-aisle.json
├── environments/         # qa.json, uat.json
├── browsers/             # Browser Profile files
├── objects/              # Object Repository
├── tests/                # Canonical Test Model files
├── suites/
├── data/
├── scripts/
├── executions/
├── reports/
└── .internal/
    └── project.db
```

Exact filenames may evolve; user-owned tests, objects, config, and scripts stay portable and committable.

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
