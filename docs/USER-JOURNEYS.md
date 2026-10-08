# V1 User Journeys

Product flows only. Boundaries: [V1-SCOPE.md](V1-SCOPE.md). Runtime: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md). Entities: [DOMAIN-MODEL.md](DOMAIN-MODEL.md).

Environment owns `baseUrl`. Application is the system under test. Browser Profile is launch config. All authoring modes write the Canonical Test Model.

## 1. Welcome / Project creation

```text
Open app → Welcome
  ├─ Recent Project → Open
  ├─ Create Project → metadata → Application → Environment → Browser Profile → Save
  ├─ Open Project
  └─ Open folder
```

**End:** Project exists locally and is ready to configure or author tests.

**Future / V1.5:** in-app Clone repository (Git-aware IDE). V1 can still open a folder that was cloned externally.

## 2. Application configuration

```text
Project → Application (name, type=WEB)
        → Environment (name, baseUrl, variables, credential references)
```

Application and Environment stay separate. Do not put Environment URLs on Test Steps.

## 3. Browser configuration

```text
Browser Profile → browser · headed/headless · optional launch options
```

Independent of Project metadata and of Environment.

## 4. Application connection

```text
Application + Environment + Browser Profile
  → Playwright Adapter session → navigate to Environment.baseUrl
  → connectivity check → Success | Failure (actionable error)
```

## 5. Test creation

```text
Create Test Case
  Recorder ──┐
  Manual ────┼──► Canonical Test Model
  Script ────┘
```

No mode stores Playwright as the source of truth. The Test Case is the user-owned artifact; it contains ordered Test Steps. Modes need not be equally complete at every V1 milestone.

## 6. Object discovery

```text
Page → DOM inspect → element
    → Object Repository (locators, attributes, role, text, fingerprint)
```

Test Steps reference `objectId`.

## 7. Test execution

```text
Test Case → Execution Engine → Test Step
  → Wait/Retry → Object Resolver
       ├─ found → execute
       └─ miss → Healing Engine
            ├─ recovered (confidence OK) → execute + Evidence
            └─ fail → Evidence + FAIL
```

## 8. Results

```text
Execution → Step Result (status, duration, error, screenshot, healing)
         → Test Case result → Test Suite result (if any)
```

## 9. Regression

```text
Test Cases → Test Suite → Environment + Browser Profile → Execute → report
```

## Future: AI (not V1)

```text
Natural language → planner → Canonical Test Model → Execution → (optional) failure analysis
```

AI must not skip the Canonical Test Model or deterministic Execution controls.
