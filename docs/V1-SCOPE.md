# V1 Scope

Product boundary for QA Automation Studio. Architecture: [V1-ARCHITECTURE.md](V1-ARCHITECTURE.md). Journeys: [USER-JOURNEYS.md](USER-JOURNEYS.md).

## Purpose

Desktop-first, local-first test automation IDE. V1 delivers a reliable **Web UI** workflow on the user’s machine using Playwright as the browser automation engine (behind a Browser Adapter). Cloud, mobile, API, collaboration, and advanced AI are not V1.

## V1 product definition

A local Electron app where a user can create a Project, configure Application / Environment / Browser Profile, author tests (Recorder, Manual, Script → Canonical Test Model), execute them locally, and inspect results and Evidence.

**Principles:** desktop-first · local-first · Web UI first · Playwright V1 engine · TypeScript · Canonical Test Model independent of Playwright · modular · deterministic automation before AI · Git-friendly project files · credentials never in test definitions.

## Core V1 user journey

Create/open Project → Application → Environment → Browser Profile → connect → Test Case (Recorder / Manual / Script) → Object Repository → assertions / variables → execute (wait/retry, Object Resolver, deterministic Healing Engine) → Evidence → PASS/FAIL → Test Suite → reports/history.

## P0 / V1 capabilities

| Area | In V1 |
| --- | --- |
| Shell | Desktop app, welcome, recent projects |
| Project | Create, open, open folder, persist, metadata |
| Config | Application, Environment, Browser Profile, connect to web app |
| Authoring | Test Case, Recorder, Manual/keyword designer, TypeScript script editor |
| Model | Canonical Test Model, Object Repository, core web keywords, assertions, variables |
| Run | Playwright via Playwright Adapter, wait/retry, deterministic self-healing |
| Output | Screenshots, logs, Evidence, Test Suites, basic reports/history |

## V1.5 / Future

| Horizon | Capabilities |
| --- | --- |
| **V1.5** | CSV/Excel data-driven tests, stronger locator/visual healing, custom keywords, reusable functions, parallel execution, richer HTML/PDF reports, Test Suite Collections, API testing, DB validation, Git-aware IDE workflows (clone/sync in-app) |
| **Future** | AI generation / NL→test / failure diagnosis / AI healing, visual testing, mobile, native desktop automation, remote/cloud execution, CI orchestration, collaboration, RBAC, central dashboard, distributed execution |

## Out of scope (V1)

Mobile · native desktop automation · full API or DB testing platform · cloud or distributed execution · team collaboration · enterprise RBAC · centralized project hosting · advanced AI agent workflows.

Do not pull these in without a formal product/architecture decision.

## V1 success criteria

A user can **repeatedly**, without engineering intervention:

Create a Project → configure Application + Environment + Browser Profile → create a login Test Case using a supported authoring mode (Recorder, Manual/Keyword, or Script) → execute locally → assert → capture Evidence → recover from a simple locator change using deterministic healing → inspect the result.

V1 includes all three authoring modes; they need not be equally complete at every milestone. All of them write the Canonical Test Model.

## Scope decision rule

Add a feature only if it (1) completes the core V1 journey, (2) improves reliability of that journey, or (3) establishes a reusable platform capability required for that journey. Otherwise defer.
