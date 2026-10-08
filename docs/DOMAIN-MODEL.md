# Domain Model

Entities and relationships only. Architecture: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md). Persistence split (files vs SQLite): [TECH-STACK.md](TECH-STACK.md).

The **Canonical Test Model** is the Playwright-independent representation of a Test Case (ordered Test Steps, assertions, variables). It is not an extra entity.

A Test Case is the user-owned executable artifact. Test Step is a domain/model concept **inside** that Test Case (stable `stepId`), not a required top-level Project file or SQLite table.

```text
Test Case
  └── ordered Test Steps
        ├── stepId
        ├── action
        ├── target          (objectId → Test Object)
        └── assertion / data / control as applicable
```

## Relationship diagram

```text
Project
 ├── Application
 ├── Environment              (belongs to Application; owns baseUrl)
 ├── Browser Profile
 ├── Object Repository
 │      └── Test Object
 ├── Test Case                (user-owned artifact)
 │      └── ordered Test Steps (nested; target = objectId)
 ├── Test Suite               (ordered Test Case ids)
 ├── Test Data
 └── Execution
        ├── Step Result
        └── Evidence
```

Project, Application, Environment, and Browser Profile are **separate**. Credentials are **references** on Environment, never stored on Test Case / Test Step.

## Entities

| Entity | Responsibility | Key fields |
| --- | --- | --- |
| **Project** | User automation workspace. Does not inline all children. | `projectId`, `name`, `description`, `type`, `location`, `version`, `createdAt`, `updatedAt` |
| **Application** | System under test (identity/type). V1 type: `WEB`. Future: `API`, `MOBILE`, `DESKTOP`, `HYBRID`. | `applicationId`, `projectId`, `name`, `type`, `description` |
| **Environment** | Execution environment for an Application (QA, UAT, Staging, Production). Owns environment URL and secret refs. | `environmentId`, `applicationId`, `name`, `baseUrl`, `variables`, `credentialReferences` |
| **Browser Profile** | Browser launch/runtime configuration. Keep V1 minimal. | `browserProfileId`, `name`, `browser`, `headless`, `viewport`, `launchOptions` |
| **Test Case** | User-owned executable artifact. Contains an ordered collection of Test Steps. | `testCaseId`, `projectId`, `name`, `description`, `status`, `variables`, `steps`, `createdAt`, `updatedAt` |
| **Test Step** | One intent in the Canonical Test Model, nested in its Test Case. Not independently persisted unless a future requirement says so. References `objectId`, not copied locators. | `stepId`, `action`, `target` (plus assertion/data/control as applicable) |
| **Test Object** | Page element with locator strategies + fingerprint for the Healing Engine. | `objectId`, `name`, `page`, `elementType`, `locators`, `attributes`, `text`, `fingerprint`, `metadata` |
| **Object Repository** | Addressable collection of Test Objects. | Logical grouping; objects keyed by `objectId` |
| **Assertion** | Expected condition after/during a step. | Type + expected value (see kinds below) |
| **Test Suite** | Ordered set of Test Cases. | `suiteId`, `name`, `description`, `testCaseIds`, `executionOrder` |
| **Test Data** | Data consumed by tests. V1 mentions test variables, Environment variables, and credential references. Future: CSV, Excel, DB, generated, external. | Bindings used at Execution (schema later) |
| **Execution** | One run of a Test Case or Test Suite. History is immutable after completion (annotations excepted). | `executionId`, `projectId`, `suiteId`, `testCaseId`, `environmentId`, `browserProfileId`, `status`, `startedAt`, `completedAt`, `duration` |
| **Step Result** | Outcome of one Test Step. | `stepId`, `status`, `duration`, `locatorUsed`, `healed`, `error`, `timestamp` |
| **Evidence** | Artifacts for a step/run (including healing). | screenshot, trace, console log, network info, DOM snapshot, execution log, healing record |

### Test Step actions (V1)

`navigate` · `click` · `fill` · `select` · `hover` · `wait` · `screenshot` · `assert`

Example (Canonical Test Model, not Playwright):

```json
{
  "stepId": "step-001",
  "action": "click",
  "target": { "objectId": "loginButton" }
}
```

### Locator strategies (on Test Object)

`testId` · `role` · `name` · `id` · `css` · `xpath` · `text`

### Assertion kinds (V1)

element exists / visible / enabled · text equals / contains · URL equals · title equals · value equals

### Execution status

`QUEUED` · `RUNNING` · `PASSED` · `FAILED` · `BLOCKED` · `CANCELLED` · `ERROR`

## Domain rules

- Test Case is the persisted executable artifact; Test Steps are ordered members with a stable `stepId` inside that Test Case.
- Test Steps reference Test Objects by `objectId`.
- Credentials: references only; never inside test definitions.
- Environment URLs do not belong on Test Steps; Application does not replace Environment.
- Browser Profile is not Project config.
- Canonical Test Model must not depend on Playwright types or APIs.
- User-owned Project artifacts are Git-friendly files; SQLite is not their source of truth ([V1-ARCHITECTURE.md](V1-ARCHITECTURE.md)).
- Variable resolution and precedence will be defined as part of the Test Data / variable implementation and are intentionally not specified by the V1 architecture documents.
