# mabl — Feature R&D Map

**Research question:** What features does mabl provide, and in what way does mabl provide / implement each feature?

**Primary sources:** [mabl Help](https://help.mabl.com/), [mabl Docs](https://docs.mabl.com/), [mabl API reference](https://api.help.mabl.com/). External sources are labeled **External source**.

**Scope:** Platform capabilities as documented for browser, mobile (native), mobile web, API, performance, and supporting execution/DevOps surfaces. Not a marketing summary or feature checklist.

---

## 1. Research Scope

mabl is documented as a cloud-oriented, low-code test automation platform centered on the **mabl Trainer** (Desktop App), **step-based tests** (not user-facing “scripts”), **Flows** for reuse, **Plans** for grouped execution, and **Applications / Environments** for targets and variables. AI appears in authoring (mabl agent), element/visual finding (Visual Assist / visual find), validation (visual assertions), maintenance (standard + advanced auto-heal), results (conversational analysis), and CLI/MCP integrations.

**Documented test types:** Browser (desktop or mobile web), Mobile (native Android/iOS), API, Performance (load wrapper over functional tests). Default workspace tests include link crawler and visit-home-page style checks per Insights docs.

**Underlying engines:** Public help specifies **Chrome** for Trainer/local runs, cloud browsers **Chrome, Firefox, Safari (WebKit), Edge**; accessibility checks use **axe-core** defaults; generative features are built on **Google Cloud enterprise AI** (per help). **Selenium** appears only in **import** (WebDriver session capture). **Playwright** appears as **locator syntax** and **import/migration**, not as the documented default browser runner. Native mobile automation engine is **not specified** in public documentation (Appium snippets are documented for custom mobile logic).

---

## 2. mabl Automation Model

Verified relationships (terminology from help):

```text
Workspace
  → Application (+ Environment) → application targets (app.url | api.url | mobile build)
  → Test (Browser | Mobile | API | Performance)
       → authored in Trainer or API Test Editor (or agent/MCP/CLI)
       → ordered Steps (recorded interactions, assertions, waits, API/DB/Mailbox, Flows, JavaScript)
       → saved test definition in workspace
  → Flow (reusable step sequence; browser / Android / iOS specific)
  → Plan (tests + stages + browser/mobile settings + triggers)
       → Plan run / Deployment event → cloud or local execution
       → Results (step logs, screenshots, diagnostics) + Insights / Coverage analytics
```

**Agentic mobile outline:** Help describes **test intent** → AI outline of **tasks**, **visual assertions**, and optional **Flows** → user records steps inside tasks in Trainer ([Create tests with generative AI](https://help.mabl.com/hc/en-us/articles/31649455424660-Create-tests-with-generative-AI)).

**Performance:** Separate **Performance test** artifact references existing browser/API tests and load settings; execution spins **virtual users** that loop functional tests ([How performance test execution works](https://help.mabl.com/hc/en-us/articles/19078530613396-How-performance-test-execution-works)).

---

## 3. Feature Map

### Test authoring

| Mode | What it does | How mabl provides it | Artifact |
|------|----------------|----------------------|----------|
| Manual Trainer | Record/edit steps against live app | New test → **Create manually** → Trainer; Rec captures interactions; + menu adds steps | Browser/Mobile test with steps |
| Agentic (browser) | NL prompt → executable test | **Build tests > Start chatting**; workspace context; outline → **Generate** (cloud) or **Generate against local app** (Desktop App); validates while building | Browser test; sessions on **Agents > Tasks** |
| Agentic (mobile/API) | Intent → outline/steps | Prompt + API spec (API); mobile outline with tasks/assertions | Mobile/API test |
| In-test generation | Add steps via NL | Trainer **Generate** (browser) | Steps appended to test |
| API editor | Request/response tests | **New test > API test** → **API Test Editor**; step builder or cURL import | Compressed API test definition (size limit ~1MB documented) |

**Evidence:** [Creating a browser test](https://help.mabl.com/hc/en-us/articles/19078188006548-Creating-a-browser-test), [Agentic test authoring for web apps](https://help.mabl.com/hc/en-us/articles/38361400751380-Agentic-test-authoring-for-web-apps), [Creating API tests](https://help.mabl.com/hc/en-us/articles/19078205090964-Creating-API-tests).

**Workflow (manual browser):**
```text
Configure test (name, app URL, credentials, DataTable)
→ Launch Trainer → interact (Rec on) → steps materialize
→ Add assertions/flows/variables → Save → Run (cloud/local)
```

---

### Trainer / recording

**What it does:** Captures user interactions and maps them to editable steps; supports bulk edit, flows, conditionals, API/DB/Mailbox, MFA, accessibility, custom finds.

**How mabl provides it:** Desktop App Trainer window (browser or device pane + step list). Recording captures clicks, text entry, navigation, many widgets; mobile adds device toolbar actions (home, back, orientation, etc.). First session Rec on; later edits Rec off by default ([Interacting with mobile app](https://help.mabl.com/hc/en-us/articles/22904768650900-Interacting-with-your-mobile-app-in-the-mabl-Trainer), [Adding and editing steps](https://help.mabl.com/hc/en-us/articles/22904771393300-Adding-and-editing-steps-in-the-mabl-Trainer)).

**Limits (documented):** No Chrome DevTools/extension/incognito control; lazy-load scroll limitations; mobile cloud installs only app under test; limited physical hardware/network services ([Interacting with web app](https://help.mabl.com/hc/en-us/articles/19078186552980-Interacting-with-your-web-app-in-the-mabl-Trainer)).

---

### Element finding

**What it does:** Resolves DOM/mobile element for interaction/assertion.

**How mabl provides it (layers):**

1. **Recorded find:** Captures 30+ attributes into **element history** ([Investigate changes for a find step](https://help.mabl.com/hc/en-us/articles/19083854354324-Investigate-changes-for-a-find-step)).
2. **Configure Find:** User weights important attributes + timeout (up to 15 min); optional auto-heal on timeout ([Configuring find steps](https://help.mabl.com/hc/en-us/articles/19078181126932-Configuring-find-steps)).
3. **Visual find (Visual Assist):** Box draw → screenshot region → **GenAI natural-language description** → at run time description + screenshot → model returns **bounding box** → resolve interactable element ([Visual find](https://help.mabl.com/hc/en-us/articles/38058551072532-Visual-find)). Clicks may auto-capture element description as fallback ([Finding the correct element](https://help.mabl.com/hc/en-us/articles/19078165693588-Finding-the-correct-element-in-browser-tests)).
4. **Custom find:** CSS, XPath, or **Playwright locator syntax**; no auto-heal; optional **visual fallback** ([Finding the correct element](https://help.mabl.com/hc/en-us/articles/19078165693588-Finding-the-correct-element-in-browser-tests)).
5. **Shadow DOM:** Trainer records shadow parent + extra strategy; open shadow only; no XPath inside shadow ([Testing in the shadow DOM](https://help.mabl.com/hc/en-us/articles/19078157363348-Testing-in-the-shadow-DOM)).

**OCR:** Not verified as OCR in official documentation; visual find uses GenAI on screenshots, not documented Tesseract/OCR pipeline.

---

### Adaptive auto-heal

**What it does:** Recovers when UI changes break original element match.

**How mabl provides it:** After Configure Find timeout (if auto-heal enabled), expands search using element history/strategies; **standard auto-heal** in cloud/local; **advanced auto-heal** (generative AI semantic match) in **cloud plan runs** after test passed in plan ≥5 times ([How auto-heal works](https://help.mabl.com/hc/en-us/articles/19078583792404-How-auto-heal-works)). Low-confidence match → step fails. **Find summary** tab logs confidence.

**Model updates:** Element history updates + **auto-heal insights** only when (1) plan run, (2) test ultimately passes. Ad-hoc/local passes do not persist model changes. Local runs use cloud models; advanced auto-heal cloud-only.

**Workflow:**
```text
Step find → no strong match within timeout
→ standard strategies (+ advanced AI in eligible cloud plan runs)
→ candidate selected with confidence
→ step runs or fails
→ (plan pass) element history / insight updated
```

---

### Visual Assist / visual testing

| Capability | Input | Processing | Output |
|------------|-------|------------|--------|
| Visual find | Trainer screenshot crop | GenAI description + runtime screenshot | Bounding box → element/action |
| Visual fallback | Locator fails | Same as visual find | Backup locate |
| Visual assertions | Element/page **screenshot** | NL description + generated **criteria** sent to LLM | Pass/fail (not DOM-based) |
| Screen assertion | Full page screenshot | Documented under assertions | Layout/content check |

Visual assertions: max **30/test** in execution; fail in performance tests; CLI local needs `--allow-billable-features` ([Creating visual assertions](https://help.mabl.com/hc/en-us/articles/28810650854292-Creating-visual-assertions)). **Not OCR** unless future docs say otherwise.

---

### Intelligent Wait

**What it does:** Waits for actionable elements using learned timing per environment.

**How mabl provides it:** From first passing **cloud** run, collects timing history; waits for appearance **and actionability** (disabled/hidden/covered/moving). Segmented per environment. Does **not** apply to static waits, URL visits, or Configure Find timeouts (those dominate). **Actionability checks** configurable at app/step level ([How Intelligent Wait works](https://help.mabl.com/hc/en-us/articles/19078583820948-How-Intelligent-Wait-works)).

---

### AI / agentic (test creation & maintenance)

Documented AI stack ([How mabl enhances your testing with AI](https://help.mabl.com/hc/en-us/articles/26881384186004-How-mabl-enhances-your-testing-with-AI)): **generative AI** (Google Cloud enterprise; customer data not used for training per help), **probabilistic expert systems** (Intelligent Wait, auto-heal), **unsupervised ML** (coverage/insights/accessibility clustering).

| Feature | User input | AI/agent behavior | Output |
|---------|------------|-------------------|--------|
| Agentic browser authoring | NL prompt + optional files/context | Searches workspace; builds outline; runs/reworks steps in cloud or local headless | Browser test |
| Agentic mobile/API | Test intent (+ API info) | Outline/tasks or API steps | Test in Trainer/editor |
| Trainer Generate | NL in session | Step generation | Steps |
| Visual assertions | NL + optional variables | Generates criteria; evaluates screenshots | Assertion step |
| Advanced auto-heal | (automatic) | Semantic attribute/text matching | Recovered find |
| Visual find | Box on UI | Description from crop | Find strategy |
| GenAI Script Generation | NL for JS need | Iterative snippet generation | JavaScript step |
| GenAI DB Query Generation | NL + DB metadata | SQL/NoSQL query draft | Database query |
| Conversational results analysis | Questions on failure | Pulls screenshots, logs, history, network | Analysis + PDF export |
| mabl MCP (cloud/local) | NL in IDE | Tools for runs, results, authoring, `#analyze_failure` | Workspace actions |

**Agent limits:** Unsupported interactions prompt manual steps ([Agentic test authoring capabilities](https://help.mabl.com/hc/en-us/articles/37947539774612-Agentic-test-authoring-capabilities)). Local MCP deprecated May 18, 2026 per [local MCP setup](https://docs.mabl.com/docs/mabl-mcp/set-up-the-local-mcp-server.html).

---

### Browser testing

Single **browser test** type; desktop default viewport 1080×1440 (configurable). Steps include navigation, forms, files, cookies, frames, tabs (Wait for tab / Switch context), JavaScript, conditionals, API steps, PDF/Mailbox, accessibility, MFA, downloads. **API steps inside browser tests** documented separately from API tests ([Creating API requests](https://help.mabl.com/hc/en-us/articles/19078231680276-Creating-API-requests) cross-link).

**Cross-browser execution:** Plan/ad hoc cloud selects Chrome, Firefox, Safari (WebKit), Edge; multi-browser multiplies runs ([Configuring browser settings](https://help.mabl.com/hc/en-us/articles/32273974394644-Configuring-browser-settings)). Trainer/local: **Chrome only** (Edge via browser path override). Chrome/Edge: performance data, step trace; Chrome: console logs ([Multibrowser support](https://help.mabl.com/hc/en-us/articles/17750883682964-Multibrowser-support)).

---

### Mobile testing (native)

**What it does:** UI tests on **.apk** (Android emulator build) or **.app** (iOS simulator build) on cloud virtual devices or local emulator/simulator/USB Android device.

**How mabl provides it:** **Mobile test** creation → upload/select build → cloud or local training → Trainer records taps/swipes/text; WebViews supported with inspectable flag documented ([Creating a mobile test](https://help.mabl.com/hc/en-us/articles/22904698525588-Creating-a-mobile-test)). Cloud run reinstalls fresh app each time. **Visual find** for tap steps ([Visual find for tap steps](https://help.mabl.com/hc/en-us/articles/38058579259156-2025-06-02-Visual-find-for-tap-steps)). Mobile add-on to core subscription per getting started.

**Underlying mobile driver:** Not specified in public documentation (Appium snippets documented for custom steps).

---

### Mobile web

**What it does:** Responsive web validation via **browser emulation**, not native app.

**How mabl provides it:** **Browser test** → **Mobile web** tab → device + orientation → Trainer uses emulated browser (viewport + mobile User-Agent). Execution can use Chrome/Safari/Firefox emulation in cloud ([Creating mobile web tests](https://help.mabl.com/hc/en-us/articles/19078197280020-Creating-mobile-web-tests)). Documented limits: no hover; visual smoke tests not supported for mobile web; Safari/Firefox emulation lacks some diagnostics.

---

### API testing

**Editor:** Requests via builder or cURL; tabs Auth, Params, Headers, Body, Pre-request JS, Settings. Assertions on status, headers, size, JSON path; default status 200 ([Validating API responses](https://help.mabl.com/hc/en-us/articles/19078221102612-Validating-API-responses)). Auth at test/flow/request: API key, Basic, Bearer, OAuth 1/2 ([Adding auth settings](https://help.mabl.com/hc/en-us/articles/25298660330516-Adding-auth-settings-to-API-tests)). AI outline on create from prompt + spec ([Getting started with API tests](https://help.mabl.com/hc/en-us/articles/17684695052308-Getting-started-with-API-tests)). **Postman** import/export documented ([Postman integration](https://help.mabl.com/hc/en-us/articles/19078236897940-Postman-integration)).

---

### Flows / reuse

**Flow:** Named subsequence with optional parameters/looping; created via + Create flow, drag-wrap, or bulk create; import into same platform test type ([Working with flows](https://help.mabl.com/hc/en-us/articles/23143432803860-Working-with-flows-in-the-mabl-Trainer)). **Auto-login flow** optional on browser test create. Plan-level shared browser state after login flow documented ([Logging into your app](https://help.mabl.com/hc/en-us/articles/19078165772564-Logging-into-your-app)).

---

### Variables / DataTables

| Mechanism | Role |
|-----------|------|
| Test/flow variables | Step outputs, JS, snippets |
| Test data-driven variables | Placeholders `{{@name}}` with training defaults |
| Environment variables | Per-environment values |
| Shared variables | Workspace-level |
| DataTable | Columns = variables, rows = scenarios; max 500 cols × 1000 rows |

**Precedence:** DataTable → shared → environment → test default → flow default ([Data-driven variables](https://help.mabl.com/hc/en-us/articles/19078251516692-Data-driven-variables)). Plans can run all/selected scenarios or ignore DataTables ([Plans](https://help.mabl.com/hc/en-us/articles/17780887930516-Plans)).

---

### Assertions

Targets: page screen, element (HTML/CSS/**visual**), variable, URL, cookie, email, download; API response assertions separate. Soft assertion behaviors: fail immediately, fail at end, continue with warning ([Assertions in the Trainer](https://help.mabl.com/hc/en-us/articles/19078158566932-Assertions-in-the-mabl-Trainer)).

---

### PDF / email

**Mailbox:** Permanent or temporary addresses; assert/open emails; match criteria; attachments → download assertions; 10 MiB / 5-minute arrival limit ([Email testing](https://help.mabl.com/hc/en-us/articles/19078159100052-Email-testing-and-validation), [Using mabl Mailbox](https://help.mabl.com/hc/en-us/articles/17753211506580-Using-mabl-Mailbox)).

**PDF:** UI-downloaded PDFs only; filename assertion; **visual assertion** on file or **mabl PDF viewer** with context switch; Safari WebKit viewer limits; no POST-download PDF ([PDF testing](https://help.mabl.com/hc/en-us/articles/19078163691412-PDF-testing)).

---

### Accessibility

**Accessibility check** step (page or element) using **axe-core** default rule sets; tags (wcag2a/aa, wcag21aa, etc.) and per-rule tuning; failure by severity; dashboard clustering ([Accessibility checks](https://help.mabl.com/hc/en-us/articles/25102056399380-Accessibility-checks), [Accessibility rules and tags](https://help.mabl.com/hc/en-us/articles/25101592214804-Accessibility-rules-and-tags)). Full results require **cloud** execution.

---

### Performance

**Performance test** wraps browser/API functional tests with **concurrency**, **duration** (≤60 min), **ramp-up**; VUs loop tests until duration; failures do not stop load run; metrics include functional failure %, Core Web Vitals (browser), step duration, API response time percentiles, HTTP error rate ([Performance testing overview](https://help.mabl.com/hc/en-us/articles/19078248319636-Performance-testing-overview)). **Coverage** page also tracks app load time / API response time from passing plan runs (Chrome/Edge) ([Monitor app performance](https://help.mabl.com/hc/en-us/articles/19083846602388-Monitor-app-performance-over-time)).

---

### AI application testing (apps under test)

**Official help (primary):** Non-deterministic output validation is implemented via **visual assertions** (screenshot + LLM criteria)—semantic/intent-style checks, not exact string match ([When to use visual assertions](https://help.mabl.com/hc/en-us/articles/31576174565268-When-to-use-visual-assertions), [Best practices for visual assertions](https://help.mabl.com/hc/en-us/articles/29849341100948-Best-practices-for-writing-visual-assertion-descriptions)). GenAI external models can be exercised via **API steps** and Postman examples folder ([How mabl enhances testing with AI](https://help.mabl.com/hc/en-us/articles/26881384186004-How-mabl-enhances-your-testing-with-AI)).

**External source:** Dedicated “AI application testing” product page describes guardrails/safety/intent assertions without separate help articles found in this research pass ([mabl.com AI application testing](https://www.mabl.com/ai-application-testing)).

**Separation:** AI **assists** test authoring/maintenance/results; **visual assertions** (and API calls) **validate** LLM-powered app behavior.

---

### Execution

| Mode | Behavior |
|------|----------|
| Cloud plan/ad hoc | Parallel default; stages sequential; browsers/devices/DataTable scenarios multiply runs |
| Deployment event | Parallel active plans matching app/env/labels ([Deployment events](https://help.mabl.com/hc/en-us/articles/17780788992148-Deployment-events)) |
| Local / CI Runner | `mabl tests run` sequential; headless; no cloud credits ([Running tests in CI](https://help.mabl.com/hc/en-us/articles/17781003105812-Running-tests-inside-a-CI-environment)) |
| `mabl tests run-cloud` | Parallel cloud |
| Mobile local | `mabl tests run-mobile` |
| Link Agent | Tunnel for private apps ([Applications and environments](https://help.mabl.com/hc/en-us/articles/17774228315284-Applications-and-environments)) |

Ad-hoc runs share environment but do not update element history/metrics like plan runs (auto-heal doc).

---

### Plans

Group tests; **stages** order execution; per-stage concurrency, failure behavior, device/DataTable overrides ([Plans](https://help.mabl.com/hc/en-us/articles/17780887930516-Plans), [Plan stage settings](https://help.mabl.com/hc/en-us/articles/19078540028820-Plan-stage-settings)). Triggers: schedule, deployment, manual; **active** toggle gates triggers.

---

### Results / debugging

Cloud failures open **conversational results analysis** (test/plan/deployment); agent uses screenshots, DOM, logs, network, history; PDF export ([Conversational results analysis](https://help.mabl.com/hc/en-us/articles/48333987247508-2026-04-21-Conversational-results-analysis)). Unavailable for local, Playwright, performance, default visit-home/link crawler per same article. Step-level artifacts on test output page ([Browser test output](https://help.mabl.com/hc/en-us/articles/19083859201556-Browser-test-output)). Element history rollback/investigate for find issues ([Investigate changes](https://help.mabl.com/hc/en-us/articles/19083854354324-Investigate-changes-for-a-find-step)).

---

### Maintenance

Mechanisms: Configure Find, element history small updates, auto-heal (standard/advanced), visual find/fallback, flows, retrain, conversational analysis, coverage **test quality** score (pass rate, stability, reliability) ([Review test quality from coverage dashboard](https://help.mabl.com/hc/en-us/articles/46637937893524-2026-02-25-Review-test-quality-from-the-coverage-dashboard)).

---

### Coverage (application / test health)

Not code coverage: **Browser tests** coverage uses link crawler + URL clustering vs pages touched by tests ([Measuring coverage in web apps](https://help.mabl.com/hc/en-us/articles/19083867227412-Measuring-coverage-in-web-apps)). **Overview** dashboard: status, quality, performance widgets ([Coverage overview dashboard](https://help.mabl.com/hc/en-us/articles/19083839661460-The-coverage-overview-dashboard)). **Insights** surface anomalies (visual changes, auto-heals, JS errors, page load timing) ([Insights](https://help.mabl.com/hc/en-us/articles/19083857000468-Insights)).

**External source:** “Active Coverage” marketing narrative (autonomous recovery, test impact analysis) on [mabl.com/active-coverage](https://www.mabl.com/active-coverage)—not fully mirrored in help articles reviewed; treat product-specific claims there as **External source** unless mapped to help features above.

---

### CI/CD, CLI, API

- **CLI:** `@mablhq/mabl-cli`; auth via API key; `deployments create`, `tests run`, `tests run-cloud`, `tests import playwright|selenium`, `datatables create`, `agent authoring|debug` commands ([mabl CLI command reference](https://help.mabl.com/hc/en-us/articles/43605432188820-mabl-CLI-command-reference)).
- **CI:** GitHub Action documented separately from GitHub integration ([GitHub integration](https://help.mabl.com/hc/en-us/articles/19084214952596-GitHub-integration-setup)).
- **REST API:** Basic auth `key:{api_key}`; deployment `POST /events/deployment`; results `GET /execution/result/event/{event_id}` ([API authentication](https://api.help.mabl.com/reference/authentication), [onDeploy](https://api.help.mabl.com/reference/ondeploy)).
- **Webhooks:** Pre/post execution JSON ([Webhooks](https://help.mabl.com/hc/en-us/articles/19084185571988-Webhooks)).

---

### Collaboration / security

Workspaces, labels, resource groups on credentials, roles (credential access). Credentials encrypted per customer key; types Basic/Cloud ± MFA ([mabl credentials](https://help.mabl.com/hc/en-us/articles/19078156933524-mabl-credentials)). Environment-specific credentials not native—workaround via environment variables ([Logging into your app](https://help.mabl.com/hc/en-us/articles/19078165772564-Logging-into-your-app)).

---

### Import / migration

| Source | Mechanism | Result |
|--------|-----------|--------|
| Playwright | `mabl tests import playwright` runtime or trace | mabl test; limitations on variables/loops/regex assertions |
| Selenium | CLI proxy :8889 captures WebDriver → cloud authoring agent | Async mabl test authoring |
| Postman | Collection 2.1 JSON import | API test(s) |

([Importing tests in CLI](https://help.mabl.com/hc/en-us/articles/26156016771988-Importing-tests-in-the-mabl-CLI), [Migrating Playwright](https://docs.mabl.com/docs/mabl-cli/migrating-playwright-tests.html)).

**Playwright/Selenium relationship:** Import/authoring integration—not documented as mabl’s primary execution engine.

---

## 4. Capability → How mabl Provides It (summary)

| Capability | What it does | How mabl provides it | Artifact / output | Platform | Underlying (if documented) | Evidence |
|------------|--------------|----------------------|-------------------|----------|----------------------------|----------|
| Browser functional test | E2E web validation | Trainer steps + cloud/local runner | Browser test, plan run results | Cloud + local CI | Chrome train/run; multi-browser cloud | [Creating a browser test](https://help.mabl.com/hc/en-us/articles/19078188006548-Creating-a-browser-test) |
| Element locate | Target UI control | History + Configure Find + visual find + locators | Find logs / Find summary | Browser/mobile | Expert systems + GenAI (advanced heal) | [Finding correct element](https://help.mabl.com/hc/en-us/articles/19078165693588-Finding-the-correct-element-in-browser-tests) |
| Auto-heal | Survive UI changes | Expanded search + optional GenAI | Updated element history (plan pass) | Cloud plan (+ local standard) | GenAI for advanced | [How auto-heal works](https://help.mabl.com/hc/en-us/articles/19078583792404-How-auto-heal-works) |
| Intelligent Wait | Sync on ready UI | Per-env timing + actionability | Faster stable runs | Browser/mobile cloud | Probabilistic expert system | [Intelligent Wait](https://help.mabl.com/hc/en-us/articles/19078583820948-How-Intelligent-Wait-works) |
| Agentic authoring | NL → tests | mabl agent + workspace context | Test + task sessions | Cloud/local Desktop | GenAI (Google Cloud) | [Agentic web authoring](https://help.mabl.com/hc/en-us/articles/38361400751380-Agentic-test-authoring-for-web-apps) |
| Visual assertion | Semantic UI check | Screenshot + LLM criteria | Pass/fail step | Trainer/local/cloud | GenAI | [Creating visual assertions](https://help.mabl.com/hc/en-us/articles/28810650854292-Creating-visual-assertions) |
| API test | Service validation | API Test Editor | API test JSON | Cloud/local | Not specified | [Creating API tests](https://help.mabl.com/hc/en-us/articles/19078205090964-Creating-API-tests) |
| Native mobile | App UI | Trainer on virtual/USB device | Mobile test | Cloud/local | Not specified | [Creating a mobile test](https://help.mabl.com/hc/en-us/articles/22904698525588-Creating-a-mobile-test) |
| Performance load | Load on journeys | VUs loop functional tests | Performance metrics | Cloud only | Same functional runners | [Performance execution](https://help.mabl.com/hc/en-us/articles/19078530613396-How-performance-test-execution-works) |
| a11y check | WCAG-related rules | axe-core step | Violation report | Browser cloud (full) | axe-core | [Accessibility checks](https://help.mabl.com/hc/en-us/articles/25102056399380-Accessibility-checks) |
| Data-driven | Multi-input runs | DataTable scenarios | N runs per plan | Plans | — | [Data-driven testing](https://help.mabl.com/hc/en-us/articles/17688665285908-Getting-started-with-data-driven-testing) |
| CI trigger | Gate deployments | Deployment event API/CLI | Event + plan runs | Cloud | REST | [Deployment events](https://help.mabl.com/hc/en-us/articles/17780788992148-Deployment-events) |
| MCP | IDE agent access | Cloud MCP HTTPS + tools | Runs/analysis/edits | Cloud | Hosted MCP | [Cloud MCP setup](https://docs.mabl.com/docs/mabl-mcp/set-up-the-cloud-mcp-server.html) |

---

## 5. Key Architectural Observations

1. **Tests are step lists**, authored primarily in Trainer or API Editor, stored in workspace—not marketed as a general-purpose code framework.
2. **Element intelligence** combines recorded attribute history, configurable find policy, Intelligent Wait, layered auto-heal, and optional GenAI visual find; custom locators trade auto-heal for precision unless visual fallback is set.
3. **Plan runs vs ad-hoc/local** differ in persistence of healing insights and advanced auto-heal eligibility.
4. **AI splits three ways:** authoring agent, visual/semantic validation (screenshots), and failure analysis; distinct from axe-based accessibility scanning.
5. **Mobile web ≠ native mobile:** same browser test type with emulation vs separate mobile test + build artifacts.
6. **Performance** reuses functional tests as load drivers; visual assertions explicitly incompatible with performance runs.
7. **Developer surface:** CLI triggers local/cloud runs; API deployment events; MCP for AI clients; Playwright/Selenium paths target **migration**, not runtime parity.
8. **Coverage** in mabl means page/journey health analytics and crawler-based page coverage, not statement/branch code coverage.

---

## 6. Research Gaps

| Topic | Status |
|-------|--------|
| Native mobile automation engine (Appium/WebDriver/etc.) | Not specified in public documentation |
| Exact browser automation stack for cloud Firefox/Safari/Edge | Not specified (Chrome training path documented) |
| OCR as a named pipeline | Not verified in official documentation |
| Dedicated help articles for “AI application testing” product positioning | Limited to visual assertions + API patterns; extended claims mainly **External source** (mabl.com) |
| “Active Coverage” autonomous mid-run recovery / test impact analysis | Marketing **External source**; partial overlap with auto-heal, insights, agent analysis in help |
| Unified reporting including external Playwright runs | Mentioned in **External source** press content; not verified in help during this pass |
| Closed shadow DOM | Documented as unsupported |
| Real iOS devices in cloud | Simulator builds only; USB real device documented for **local Android** training |

---

*Second-pass searches performed across auto-heal, Visual Assist/visual find, Intelligent Wait, agentic creation, DataTables, Flows, PDF/email, accessibility, performance, Active Coverage/coverage dashboards, CLI, API, MCP, Playwright/Selenium import, and mobile vs mobile web.*
