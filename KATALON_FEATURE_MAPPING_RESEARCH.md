# Katalon Feature-Mapping Research (Official Documentation)

**Research question:** What features does Katalon provide, and in what way does Katalon provide / implement each feature?

**Primary source:** [Katalon Docs](https://docs.katalon.com/) (all factual claims below are tied to cited official pages unless marked **External source**).

**Scope:** Katalon Studio, Katalon Runtime Engine (Test Execution – Local), Katalon True Platform (formerly TestOps), Test Execution – Cloud, Recorder/Spy utilities, and documented platform capabilities.

**Comparison note:** Sections distinguish **underlying engine** vs **Katalon product layer** where documentation states it.

---

## Documented product inventory (high level)

| Product / area | Role (per docs) | Evidence |
|----------------|-----------------|----------|
| **Katalon Studio** | IDE for creating/executing web, API, mobile, desktop tests; built on Selenium (web) | [About Katalon Studio](https://docs.katalon.com/katalon-studio/about-katalon-studio) |
| **Katalon Runtime Engine (KRE)** | CLI/console execution for CI/CD (no full IDE UI) | [Get started with KRE](https://docs.katalon.com/katalon-studio/execute-tests/katalon-runtime-engine/get-started-with-katalon-runtime-engine) |
| **Katalon True Platform** | Unified planning, execution, management, analytics (includes former TestOps) | [About Katalon True Platform](https://docs.katalon.com/katalon-platform/about-katalon-true-platform) |
| **Test Execution – Cloud** | Cloud browsers/devices/OS (formerly TestCloud) | [About Katalon True Platform](https://docs.katalon.com/katalon-platform/about-katalon-true-platform) |
| **Katalon AI Assistant** | In-IDE AI (Ask/Agent, MCP, inline codegen) | [Katalon AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview) |
| **Katalon TrueTest** | Production journey capture → AI-generated tests | [Katalon Docs home](https://docs.katalon.com/) |
| **Katalon Store** | Plugin marketplace for Studio | [Install plugins from Katalon Store](https://docs.katalon.com/katalon-studio/katalon-store/install-plugins-online-from-katalon-store) |
| **Browser extensions** | Recording Engine / Recorder add-ons for Active Browser recording | [Recording Engine Extension](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/katalon-studio-recording-engine-extension) |

---

## Platform / engine relationship (summary)

| Capability area | Underlying engine (documented) | Katalon product layer (documented) |
|-----------------|--------------------------------|-------------------------------------|
| Web UI automation | Selenium WebDriver / Selenium 4 (BiDi for Smart Wait/Locator in supported browsers) | Keywords (`WebUI.*`), Object Repository, Recorder/Spy, Smart Wait, self-healing, Time Capsule, execution profiles |
| Mobile | Appium 3.x + drivers (UiAutomator2, XCUITest); KS starts/manages Appium | Mobile Recorder/Spy, mobile keywords, cloud device path, image locators (Base64 + Appium image detection) |
| Windows desktop | FlaUI-based custom driver (10.4.0+); legacy WinAppDriver path documented for older flows | Windows/Native Windows Recorder, `Windows.*` keywords, desired capabilities, coordinate offset recording |
| API | HTTP client stack in Studio (REST/SOAP/GraphQL); imports from OpenAPI/WSDL/Postman/SoapUI | Web Service Request objects, API Collections, AI API test generation (beta) |
| BDD | Cucumber/Gherkin in project | Feature files under `Include/features`, step definitions in Groovy, `CucumberKW.*` keywords |
| Visual regression | Platform compares baseline vs checkpoint screenshots | Pixel / layout (AI zones) / text-content methods; ignore zones — **True Platform**, not local Studio execution alone |
| AI self-healing | LLM (configurable provider/model) | Classic multi-locator fallback first; then AI analyzes PAGE_SOURCE, ACCESSIBILITY_TREE, screenshots |

---

# A. Test creation / authoring

### Manual view (keyword-driven test steps)

**What it does**  
Builds automated tests as rows of built-in/custom keywords with object, input, output, and description—without writing Groovy directly.

**How Katalon provides it**  
Test case editor **Manual** tab; steps map to executable keyword invocations. Manual and **Script** views stay synchronized (edits in one view reflect in the other). Supports intention groups, Call Test Case, statements, and failure-handling per step.

**Typical workflow**
```text
Open test case → Manual tab → Add keyword + configure Object/Input
→ Save → Run with chosen environment
→ (optional) Switch to Script view to inspect generated Groovy
```

**Evidence:** [Manual view](https://docs.katalon.com/katalon-studio/create-test-cases/generate-test-steps-in-katalon-studio-manual-view), [Script view sync](https://docs.katalon.com/katalon-studio/create-test-cases/generate-test-steps-in-katalon-studio-script-view)

---

### Script view (Groovy / Java)

**What it does**  
Code-level test authoring using Groovy or Java, Katalon built-in keyword aliases (`WebUI`, `Mobile`, `WS`, `CucumberKW`, etc.), and custom keywords.

**How Katalon provides it**  
Manual steps translate to script on view switch; users can author directly, use AI inline Generate/Explain, or refine recorded tests.

**Typical workflow**
```text
Script tab → write/import Groovy → findTestObject('Object Repository/...')
→ WebUI/Mobile/WS keyword calls → Run/Debug
```

**Evidence:** [Script view](https://docs.katalon.com/katalon-studio/create-test-cases/generate-test-steps-in-katalon-studio-script-view), [Create test case overview](https://docs.katalon.com/katalon-studio/create-test-cases/create-test-case-overview)

---

### Keyword-driven model & reusable steps

**What it does**  
Tests are sequences of keywords (built-in or custom); supports **Call Test Case** and **custom keywords** (`@Keyword` Groovy/Java methods).

**How Katalon provides it**  
Keywords Browser in IDE; custom keywords live under `Keywords/`; invoked via `CustomKeywords.'package.Class.method'(...)`.

**Evidence:** [Custom keywords intro](https://docs.katalon.com/katalon-studio/keywords/custom-keywords/introduction-to-custom-keywords-in-katalon-studio)

---

### BDD (Cucumber / Gherkin)

**What it does**  
Behavior described in `.feature` files; step definitions link Gherkin to executable Groovy.

**How Katalon provides it**  
`File > New > BDD Feature File`; step definitions in `Keywords` or `Include/scripts/groovy`; run via `CucumberKW.runFeatureFile(...)` or right-click **Run Feature File**. Hooks supported. Reports can upload to True Platform.

**Typical workflow**
```text
.feature scenarios → Groovy @Given/@When/@Then step defs
→ Test case calls CucumberKW.runFeatureFile
→ Execution → BDD report (Studio / platform)
```

**Evidence:** [BDD framework](https://docs.katalon.com/katalon-studio/bdd-testing/bdd-testing-framework-cucumber-integration), [Feature files](https://docs.katalon.com/katalon-studio/bdd-testing/work-with-bdd-feature-files-in-katalon-studio)

---

### Parameterization & variables

**What it does**  
Test case variables, global variables, execution profile variables, and encrypted text for secrets.

**How Katalon provides it**  
Variables tab on test cases; profiles under `Profiles/`; `setEncryptedText` for passwords; CLI override `g_<varName>=` in KRE.

**Evidence:** [Data-driven testing](https://docs.katalon.com/katalon-studio/data-driven-testing/data-driven-testing-with-katalon-studio), [KRE CLI](https://docs.katalon.com/katalon-studio/execute-tests/katalon-runtime-engine/command-line-syntax-in-katalon-runtime-engine)

---

### Test case organization

**What it does**  
Folders for test cases, test suites, object repository (POM-style recommended), test listeners/fixtures.

**How Katalon provides it**  
Tests Explorer; artifacts stored as project files (e.g. `.tc`, `.groovy`, object `.rs`).

**Evidence:** [Best practices](https://docs.katalon.com/katalon-studio/get-started/katalon-studio-best-practices), [Git: commit Test Cases, Suites, Object Repository](https://docs.katalon.com/katalon-studio/manage-projects/project-settings/git-integration/work-with-git-in-katalon-studio)

---

# B. Recording / spying / object capture

### Web Recorder (classic) & Web Recorder Plus

**What it does**  
Records user interactions in a browser and generates keyword steps plus captured web test objects.

**How Katalon provides it**  
**Record Web** toolbar → URL + browser mode (New / Active / custom capabilities). Actions appear in **Recorded Actions**; elements in **Captured Objects**. Password fields use **Set Encrypted Text**. Plus adds AI Recording Agent (beta), advanced object configuration, Flutter/canvas/shadow DOM scenarios, Smart Web Inspectors option.

**Typical workflow**
```text
Record Web → interact with AUT → steps + objects captured
→ Save script → choose Object Repository folder / duplicate handling
→ Test case (.tc) + Object Repository entries persisted
```

**Evidence:** [Record Web](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/record-web-utility-in-katalon-studio), [Web Recorder Plus](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/katalon-web-recorder-plus)

---

### Spy Web

**What it does**  
Captures individual web elements and locators without full scenario recording.

**How Katalon provides it**  
**Spy Web** → Start browser → hover (highlight + XPath overlay) → right-click **Capture** → **Save** to Object Repository. Supports verify/highlight in Spy.

**Typical workflow**
```text
Spy Web → capture element(s) → edit locators in Spy
→ Save → reusable Test Object in Object Repository
→ Manual/script steps reference findTestObject('...')
```

**Evidence:** [Spy Web](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/spy-web-utility-in-katalon-studio)

---

### Smart Mobile Recorder

**What it does**  
Records mobile interactions; optional **Interactive Mode** on live device view; playback inside session.

**How Katalon provides it**  
Uses Appium session (local, remote, or cloud). Auto-creates mobile test objects; heartbeat against Appium timeout; auto-refresh ~60s. Locator validation modes: None / Default Locator / All Locators.

**Typical workflow**
```text
Record Mobile → select device/app → Interactive Mode actions
→ steps + Mobile objects → Save test case
```

**Evidence:** [Smart Mobile Recorder](https://docs.katalon.com/katalon-studio/record-and-spy/mobile-record-and-spy-utilities/smart-mobile-recorder), [Mobile quick start](https://docs.katalon.com/katalon-studio/get-started/quick-start-guide-for-mobile-testing)

---

### Mobile Spy

**What it does**  
Capture/verify mobile objects and locators (documented alongside manage mobile test objects).

**How Katalon provides it**  
Mobile Object Spy; add screenshot for image locator manually.

**Evidence:** [Manage mobile test objects](https://docs.katalon.com/katalon-studio/test-objects/mobile-test-objects/manage-mobile-test-objects-in-katalon-studio)

---

### Windows Recorder (FlaUI) & Native Windows Recorder

**What it does**  
Records desktop UI actions on Windows; Native variant mirrors web-like recording UX.

**How Katalon provides it**  
**Record Windows Action** (WinAppDriver URL + capabilities in older docs) vs **Native Windows Recorder** (exe path, absolute XPath locators, `clickElementOffset` for coordinate-based clicks). **10.4.0+:** built-in FlaUI driver on `localhost:4723` by default.

**Typical workflow**
```text
Specify .exe / title → Start → interact → Windows keywords generated
→ Windows test objects saved → execution via Windows.* keywords
```

**Evidence:** [Windows Record](https://docs.katalon.com/katalon-studio/record-and-spy/windows-record-and-spy-utilities/windows-record-utility-in-katalon-studio), [Native Windows Recorder](https://docs.katalon.com/katalon-studio/record-and-spy/windows-record-and-spy-utilities/native-windows-recorder-in-katalon-studio), [FlaUI driver](https://docs.katalon.com/katalon-studio/manage-projects/set-up-projects/windows-desktop-apps-testing/desktop-applications-testing-with-flaui-driver-in-katalon-studio)

---

### Recording Engine browser extension

**What it does**  
Enables **Active Browser** and profile-scoped recording/spy for Chrome/Edge (packed extension from store).

**How Katalon provides it**  
Install **Katalon Studio Recording Engine** per browser profile; configure profile in Desired Capabilities or Preferences.

**Evidence:** [Recording Engine Extension](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/katalon-studio-recording-engine-extension)

---

# C. Object identification / Object Repository

### Test Object model (Web / Mobile / Windows / API)

**What it does**  
Named, versionable artifacts referencing AUT elements or API requests; steps resolve objects at runtime via `findTestObject("id")`.

**How Katalon provides it**  
**Object Repository** tree; each object has selection method(s), locators/properties, optional multiple alternate locators for self-healing. Runtime-only objects possible via `TestObject` API (not persisted).

**Typical workflow**
```text
Spy/Recorder/Manual New Test Object → define locators
→ Test step binds keyword + object ID
→ Engine resolves default locator → (fallback) self-healing
```

**Evidence:** [Manage web test objects](https://docs.katalon.com/katalon-studio/test-objects/web-test-objects/manage-web-test-objects), [Refactor unused objects](https://docs.katalon.com/katalon-studio/maintain-tests/refactor-test-objects-in-katalon-studio)

---

### Web locator strategies (XPath, Attributes, CSS, Image, Smart Locator)

**What it does**  
Multiple strategies to locate DOM elements; one **default** locator used at execution; others can support classic self-healing.

**How Katalon provides it**  
Project default in **Project > Settings > Test Design > Web UI**. Spy/Recorder auto-generate per strategy (e.g. prioritized XPath list, attribute-based XPath, CSS, image screenshot, Smart Locator via extension/BiDi query engine).

**Evidence:** [Selection methods](https://docs.katalon.com/katalon-studio/test-objects/web-test-objects/selection-methods-for-web-objects)

---

### Smart Locator

**What it does**  
Enhanced identification for shadow DOM, iframes, dynamic IDs, SVG.

**How Katalon provides it**  
Query engine + browser native CSS/XPath/text selectors; BiDi-native in Chrome/Firefox/Edge/headless (10.x); extension fallback where BiDi unavailable. Documented caveats for negation keywords and live-updating text.

**Evidence:** [Selection methods – Smart Locator](https://docs.katalon.com/katalon-studio/test-objects/web-test-objects/selection-methods-for-web-objects)

---

### Dynamic / runtime objects

**What it does**  
Create or alter locators during test run (e.g. CSS override via `setSelectorValue`).

**How Katalon provides it**  
Groovy API on `TestObject` / `MobileTestObject`; custom keywords.

**Evidence:** [Selection methods – CSS runtime](https://docs.katalon.com/katalon-studio/test-objects/web-test-objects/selection-methods-for-web-objects), [Mobile programmatic objects](https://docs.katalon.com/katalon-studio/test-objects/mobile-test-objects/manage-mobile-test-objects-in-katalon-studio)

---

# D. Self-healing (classic vs AI)

### Classic self-healing (multi-locator fallback)

**What it does**  
When the **default** locator fails, tries other locators already stored on the same test object before failing the step.

**How Katalon provides it**  
Triggered on lookup failure for interaction keywords (verify/wait keywords auto-excluded by default). Order of **locator methods** configurable in **Project Settings > Self-Healing** (WebUI/Mobile). Per-object default locator overrides global. Successful recovery → test continues; after run, **Self-healing Insights** lists proposals.

**Typical workflow**
```text
Execute step with findTestObject
→ default locator fails
→ try alternate locators on same object (priority order)
→ success → continue + record proposal | failure → AI or fail per FailureHandling
```

**Evidence:** [Self-healing tests](https://docs.katalon.com/katalon-studio/maintain-tests/self-healing-tests-in-katalon-studio)

---

### AI self-healing

**What it does**  
When classic healing fails, uses an **LLM** to propose a new locator from execution-time signals.

**How Katalon provides it**  
Inputs (Web): `PAGE_SOURCE`, `ACCESSIBILITY_TREE`, `FULL_PAGE_SCREENSHOT`, `ELEMENT_SCREENSHOT` (configurable; docs note PAGE_SOURCE required when enabled). Mobile: page source + screenshots. Model from **Preferences > AI Configurations** or overrides (`SELF_HEALING_AI_MODEL`, API keys, KRE flags `-execution.selfheal.web.ai.enabled`, `inputSources`). Requires **Execution Viewer** enabled. User **Approve/Discard** in Self-healing Insights; screenshot preview on success.

**Typical workflow**
```text
Classic healing exhausted
→ AI analyzes configured inputs
→ suggests locator (e.g. smart XPath; image-based recovery documented with limitations)
→ run completes → Insights table → user approves permanent locator update
```

**Evidence:** [Self-healing tests](https://docs.katalon.com/katalon-studio/maintain-tests/self-healing-tests-in-katalon-studio), [KRE AI self-heal args](https://docs.katalon.com/katalon-studio/execute-tests/katalon-runtime-engine/command-line-syntax-in-katalon-runtime-engine)

---

# E. Smart Wait / synchronization

### Smart Wait (WebUI)

**What it does**  
Reduces flaky web steps by waiting for page/element readiness beyond naive DOM presence (10.3.0+: interactivity, animations, background request filtering, mutation observers, retries on stale/not interactable).

**How Katalon provides it**  
Default on in **Project > Settings > Execution > WebUI**. Per-test `WebUI.enableSmartWait()` / `disableSmartWait()`. Implementation: browser extension in non-BiDi environments; **Selenium 4 BiDi** in Chrome/Edge/Firefox/headless (10.0+). Safari/remote/cloud may still need extension.

**Typical workflow**
```text
Before WebUI interaction keyword
→ Smart Wait layer ensures page stable / element interactable
→ WebDriver command executes
```

**Evidence:** [Smart Wait function](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/smart-wait-function), [Execution settings](https://docs.katalon.com/katalon-studio/manage-projects/project-settings/execution-settings-in-katalon-studio)

---

### Explicit waits & delays

**What it does**  
Keyword-level and project-level timing control.

**How Katalon provides it**  
`WebUI.waitForElementPresent/Visible/Clickable`, `waitForPageLoad`, global **default wait for element** timeout, **delay between actions** on selected WebDriver commands, `Delay` keyword, recorder-added sync steps.

**Evidence:** [Solving wait-time issues](https://docs.katalon.com/katalon-studio/keywords/using-keywords-in-katalon-studio/web-testing/solving-wait-time-issue-with-katalon-studio), [Sync while recording](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/how-to-use-synchronization-commands-while-recording-in-katalon-studio)

---

# F. Time Capsule / execution evidence

### Time Capsule (WebUI maintenance)

**What it does**  
Preserves AUT state when a web object cannot be found, so testers can re-spy against the failure state.

**How Katalon provides it**  
Triggered on `WebElementNotFoundException` when enabled (**Default Time Capsule**). **Chrome only** per docs. Log Viewer / suite Result → **Click here to fix broken Test Object** → Object Spy with captured state → re-capture → Save.

**Typical workflow**
```text
WebUI step fails (element not found)
→ Time Capsule snapshot captured
→ User opens fix link → Spy with frozen state → update object → re-run
```

**Evidence:** [Time Capsule](https://docs.katalon.com/katalon-studio/maintain-tests/fix-broken-web-test-objects-with-time-capsule-in-katalon-studio)

---

### Execution logs, screenshots, video

**What it does**  
Step-level logs, failure screenshots, optional video (browser GUI), HTML snapshot (Time Capsule).

**How Katalon provides it**  
**Log Viewer** (tree/text modes), **Execution Viewer** (newer reporting UI), project report settings. Feature tier matrix mentions videos, Time Capsule HTML in reports.

**Evidence:** [Explore Katalon Studio](https://docs.katalon.com/katalon-studio/get-started/explore-katalon-studio), [KS Free vs Paid features](https://docs.katalon.com/katalon-studio/katalon-studio-enterprise-and-katalon-runtime-engine-license/katalon-studio-vs-katalon-studio-enterprise-features)

---

# G. Visual / image capabilities

### Web image locator (object identification)

**What it does**  
Locates elements by comparing reference screenshot to live element rendering (pixel comparison).

**How Katalon provides it**  
**Image** selection method; **Add Screenshot** in Recorder/Spy (not default on record for performance). Uses displayed element size; recommends same resolution/device.

**Input:** DOM-rendered element bitmap vs stored image. **Not documented as OCR.**

**Evidence:** [Web image-based testing](https://docs.katalon.com/katalon-studio/test-objects/web-test-objects/web-image-based-testing)

---

### Mobile image locator

**What it does**  
Finds on-screen regions matching a reference image when hierarchy locators break.

**How Katalon provides it**  
Manual screenshot on object; Base64 encoding; **Appium image element detection** at execution.

**Evidence:** [Mobile image-based testing](https://docs.katalon.com/katalon-studio/manage-projects/set-up-projects/mobile-testing/mobile-image-based-testing-in-katalon-studio)

---

### True Platform Visual Testing (regression)

**What it does**  
Compares baseline vs checkpoint screenshots from test runs; highlights diffs.

**How Katalon provides it**  
**Pixel**, **Layout** (AI zone matching), **Text Content** (text-like zones categorized Identical/Shifted/Missing-New). Ignore zones for dynamic areas. **Platform feature**, not Studio-local analysis engine.

**Evidence:** [Visual Testing overview](https://docs.katalon.com/katalon-platform/analyze/visual-testing/visual-testing-overview), [Use Visual Testing](https://docs.katalon.com/katalon-platform/analyze/visual-testing-legacy/use-testops-visual-testing)

---

### OCR

**What it does**  
*No dedicated OCR feature documented.*

**How Katalon provides it**  
N/A. Text from DOM via `WebUI.getText`. Canvas text called out in Recorder Plus without specifying OCR API. `LLM.assertFile` sends images/PDFs to AI for **judgment**, not structured OCR extraction.

**Evidence:** [WebUI getText](https://docs.katalon.com/katalon-studio/keywords/keyword-description-in-katalon-studio/web-ui-keywords/webui-get-text), [LLM Assert File](https://docs.katalon.com/katalon-studio/keywords/keyword-description-in-katalon-studio/ai-keywords/llm-assert-file), [Web Recorder Plus](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/katalon-web-recorder-plus)

---

# H. AI features (Studio + platform)

### Katalon AI Assistant – Ask mode

**What it does**  
Chat Q&A, snippets, explanations; multiple local conversations.

**Input:** User prompts; optional **Current file** context (test case, suite, listener, WS request, keyword, feature file, etc.).

**Output:** Text/code suggestions (user validates).

**Evidence:** [AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview)

---

### Katalon AI Assistant – Agent mode (MCP)

**What it does**  
Multi-step, project-aware changes via MCP servers (Katalon, Katalon Studio, True Platform; external e.g. Atlassian, Chrome DevTools).

**Input:** Natural language + project context.

**Output:** Modified project artifacts shown in **Modified Files** for review.

**Evidence:** [AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview)

---

### Inline Generate / Explain code

**What it does**  
From Script view comments or selection, generates or explains Groovy.

**Evidence:** [AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview), [Script view](https://docs.katalon.com/katalon-studio/create-test-cases/generate-test-steps-in-katalon-studio-script-view)

---

### AI self-healing

(See section D.)

---

### AI Failure Troubleshoot & AI Failure Analysis in reports

**What it does**  
Analyzes failed runs (stack traces, logs) and explains failures in plain language (HTML/email reports).

**Where:** Post-execution / reporting.

**Evidence:** [AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview)

---

### AI-generated API tests (beta)

**What it does**  
From OpenAPI in an **API Collection**, generates and **immediately executes** tests; shows progress report **without persisting** test artifacts in project (per beta doc). Header auth only for generation.

**Typical workflow**
```text
Import OpenAPI → API Collection folder
→ Generate Test tab → select paths/methods/types → Generate Test
→ AI runs requests → transient report
```

**Evidence:** [Generate API tests with AI](https://docs.katalon.com/katalon-studio/create-test-cases/generate-api-tests-with-ai-beta)

---

### LLM keywords (e.g. assertFile)

**What it does**  
Sends files (images, pdf, txt, csv, json, log) to configured AI service; asserts plain-language expectation (AI verdict PASS/FAIL).

**Evidence:** [LLM Assert File](https://docs.katalon.com/katalon-studio/keywords/keyword-description-in-katalon-studio/ai-keywords/llm-assert-file)

---

### Prompt Library / engineering prompts

**What it does**  
Customize system prompts for Ask, Agent, codegen, explanation, failure analysis.

**Evidence:** [AI Assistant Overview](https://docs.katalon.com/katalon-studio/studioassist/studioassist-overview)

---

### True Platform AI Assistant & TrueTest

**What it does**  
Platform-level conversational QA; **TrueTest** captures production user journeys to generate tests (listed on docs home).

**Evidence:** [True Platform about](https://docs.katalon.com/katalon-platform/about-katalon-true-platform), [Katalon Docs](https://docs.katalon.com/)

---

# I. Web automation (capability map)

| Area | How Katalon exposes it | Evidence |
|------|------------------------|----------|
| Browsers | Chrome, Firefox, Safari, Edge; cross-browser suites | [Supported technologies](https://docs.katalon.com/katalon-studio/supported-technologies-for-katalon-studio) |
| Frameworks | Front-end frameworks (React, Angular, Vue) | Same |
| Headless | Supported; Smart Wait/Locator via BiDi where supported | [Smart Wait](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/smart-wait-function) |
| Navigation / clicks / input | `WebUI.*` built-in keywords | [Record Web](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/record-web-utility-in-katalon-studio) |
| JavaScript | Documented keyword set (execute JS) — see WebUI keyword catalog | [Keyword docs index](https://docs.katalon.com/katalon-studio/keywords/keyword-description-in-katalon-studio/) |
| Frames / windows / tabs | Keywords + manual scripting; some scenarios not recordable | [Record and playback limits](https://docs.katalon.com/katalon-studio/record-and-spy/webui-record-and-spy-utilities/create-test-cases-with-record-and-playback-in-katalon-studio) |
| Auth / cookies / downloads | WebUI keywords & execution settings | [Execution settings](https://docs.katalon.com/katalon-studio/manage-projects/project-settings/execution-settings-in-katalon-studio) |
| Remote / cloud | Test Execution – Cloud via CLI `-browserType=TestCloud` etc. | [Jenkins + TestCloud](https://docs.katalon.com/katalon-platform/integrations/cicd-integrations/jenkins-integration/execute-katalon-tests-on-jenkins-with-testcloud-environment) |
| Parallel | Test suite collection parallel mode | [Execute test cases](https://docs.katalon.com/katalon-studio/execute-tests/how-to-execute-test-cases) |

**Underlying engine:** Selenium ([About KS](https://docs.katalon.com/katalon-studio/about-katalon-studio)).

---

# J. Mobile automation

**What Katalon adds on Appium**  
Device/session UI (Recorder, Spy, cloud upload path), keyword layer (`Mobile.*`), object repository, locator strategies, self-healing (classic + AI), Smart Mobile Recorder UX (interactive mode, heartbeat, in-session playback), desired capabilities UI, integration with True Platform device cloud.

**Setup**  
Appium 3 + UiAutomator2 (Android) / XCUITest (iOS, macOS); path in Preferences; KS starts Appium and loads drivers.

**App types**  
Native, mobile web, hybrid (per supported technologies).

**Evidence:** [Execute mobile with Appium](https://docs.katalon.com/katalon-studio/manage-projects/set-up-projects/mobile-testing/execute-mobile-tests-with-appium), [Supported technologies](https://docs.katalon.com/katalon-studio/supported-technologies-for-katalon-studio), [Smart Mobile Recorder](https://docs.katalon.com/katalon-studio/record-and-spy/mobile-record-and-spy-utilities/smart-mobile-recorder)

---

# K. Windows / desktop automation

**Engines**  
- **10.4.0+:** FlaUI-based built-in driver (default `localhost:4723`).  
- **Native Windows Recorder:** Windows-only, license required; absolute XPath; offset clicks.  
- Legacy WinAppDriver URL path still documented for Windows Recorder configuration.

**Supported app types (docs)**  
UWP, WinForms, WPF, Win32 on Windows 10+.

**Evidence:** [FlaUI driver](https://docs.katalon.com/katalon-studio/manage-projects/set-up-projects/windows-desktop-apps-testing/desktop-applications-testing-with-flaui-driver-in-katalon-studio), [Native Windows Recorder](https://docs.katalon.com/katalon-studio/record-and-spy/windows-record-and-spy-utilities/native-windows-recorder-in-katalon-studio)

---

# L. API / web service testing

### REST / SOAP / GraphQL

**What it does**  
Create, import, parameterize, and assert API requests; chain in test cases via `WS` keywords.

**How Katalon provides it**  
**Web Service Request** objects in Object Repository; dedicated **API/Web Service** project type with import toolbar.

**Evidence:** [Introduction to WS test objects](https://docs.katalon.com/katalon-studio/test-objects/api-test-objects/introduction-to-web-services-test-object-in-katalon-studio)

---

### Imports

| Source | Process | Resulting artifact |
|--------|---------|-------------------|
| OpenAPI 2.0/3.0 JSON/YAML | Import OpenAPI dialog / OR Import | REST request objects (folder); OpenAPI → **API Collection** (newer) | [Import OpenAPI](https://docs.katalon.com/katalon-studio/test-objects/api-test-objects/import-web-service-objects/import-rest-request-from-openapi) |
| Postman collection JSON | Postman import | WS requests | [Import Postman](https://docs.katalon.com/katalon-studio/test-objects/api-test-objects/import-web-service-objects/import-restful-from-postman-to-katalon-studio) |
| WSDL | Import WSDL | SOAP requests | [WS intro](https://docs.katalon.com/katalon-studio/test-objects/api-test-objects/introduction-to-web-services-test-object-in-katalon-studio) |
| SoapUI | Import (documented in integrations table) | WS objects | [Integrations](https://docs.katalon.com/katalon-studio/integrations/integrations-in-katalon-platform) |

**Documented import gaps (OpenAPI):** no raw body from Swagger; auth not parsed; variables need manual adjustment.

---

### API Collections & auth

**What it does**  
Bulk auth (API key, Bearer, Basic, OAuth 1/2, AWS Sig V4, NTLM, Digest, etc.) inherited by child requests unless overridden.

**Evidence:** [API Collections authorization](https://docs.katalon.com/katalon-studio/test-objects/api-test-objects/authorization/api-collection-for-bulk-managing-authorization)

---

# M. Test data

### Test Data files

**What it does**  
Internal grid, Excel, CSV, DB (JDBC) sources for data-driven runs.

**How Katalon provides it**  
`File > New > Test Data`; bound via **Show Data Binding** at test case or suite level; iteration types One/Many; `findTestData` in scripts.

**Evidence:** [Manage test data](https://docs.katalon.com/katalon-studio/data-driven-testing/manage-test-data), [Manage data binding](https://docs.katalon.com/katalon-studio/data-driven-testing/manage-data-binding)

---

# N. Test suites / execution

### Test Suite

**What it does**  
Ordered (or configured) set of test cases with shared report and hooks.

**Evidence:** [How to execute](https://docs.katalon.com/katalon-studio/execute-tests/how-to-execute-test-cases)

---

### Test Suite Collection (TSC)

**What it does**  
Runs multiple suites **sequential** or **parallel** (max concurrent instances, delay between instances). Per-suite: Run with environment, profile, run flag.

**Evidence:** [Manage TSC](https://docs.katalon.com/katalon-studio/manage-test-artifacts/manage-test-suite-collections-in-katalon-studio), [Execute TSC](https://docs.katalon.com/katalon-studio/execute-tests/how-to-execute-test-cases)

---

### Execution profiles

**What it does**  
Environment-specific global variables (URLs, credentials).

**How Katalon provides it**  
`Profiles/` files; selected in suite/TSC/CLI `-executionProfile`; override `g_*` in KRE.

**Evidence:** [KRE get started](https://docs.katalon.com/katalon-studio/execute-tests/katalon-runtime-engine/get-started-with-katalon-runtime-engine)

---

### Katalon Runtime Engine

**What it does**  
Headless/console execution of suites/TSC with licensing (`-apiKey`, `-orgID`), report paths, retries, TestCloud/browser flags, AI self-heal flags.

**Typical workflow**
```text
CI agent → katalonc -projectPath=... -testSuitePath=... -browserType=...
→ reports folder → optional upload to True Platform
```

**Evidence:** [KRE CLI syntax](https://docs.katalon.com/katalon-studio/execute-tests/katalon-runtime-engine/command-line-syntax-in-katalon-runtime-engine)

---

# O. Debugging

### Debug mode (Script view)

**What it does**  
Breakpoints, step into/over/out, variables/expressions views (Eclipse-like Debug perspective).

**Difference from Run**  
Pauses at breakpoints; browser debug requires not closing session for **Debug from here**.

**Evidence:** [Debug a test case](https://docs.katalon.com/katalon-studio/debug-a-test-case/debug-a-test-case-in-katalon-studio), [Debugging best practices](https://docs.katalon.com/katalon-studio/debug-a-test-case/effective-debugging-best-practices-in-katalon-studio)

---

### Manual view debugging

**What it does**  
Enable/disable steps; **Run from here** / **Debug from here** (license for some features per docs).

**Evidence:** [Debugging best practices](https://docs.katalon.com/katalon-studio/debug-a-test-case/effective-debugging-best-practices-in-katalon-studio)

---

### Class decompiler

**What it does**  
Attach/decompile classes for breakpoint debugging into dependencies.

**Evidence:** [Class decompiler](https://docs.katalon.com/katalon-studio/maintain-tests/configure-class-file-decompilation-in-katalon-studio)

---

# P. Reporting – Local Studio vs True Platform

| Capability | **Katalon Studio (local)** | **Katalon True Platform** |
|------------|---------------------------|---------------------------|
| HTML/PDF/CSV/JUnit reports | Auto-generate to `Reports/` per project settings | Upload/sync execution results |
| Report on manual stop | Optional auto-generate | Optional auto-upload |
| Dashboards (release readiness, flakiness, coverage) | Limited / sample analytics | Seven dashboard report types |
| Visual testing baselines | — | Baseline/checkpoint comparison |
| AI failure analysis in report | Can feed Studio-generated HTML reports | Platform AI assistant for planning/execution/analysis |
| Manual test management | — | Manual testing module |

**Evidence:** [Suite reports in Studio](https://docs.katalon.com/katalon-studio/test-reports/view-test-reports/view-test-suite-and-test-suite-collection-reports-in-katalon-studio), [Upload to platform](https://docs.katalon.com/katalon-studio/test-reports/upload-test-results-from-katalon-studio-to-katalon-testops-manually), [Dashboard overview](https://docs.katalon.com/katalon-platform/analyze/reports/view-test-reports-legacy/view-testops-dashboard/testops-dashboard-overview)

---

# Q. CI/CD / DevOps

### Jenkins

**What it does**  
Freestyle or Pipeline running `katalonc` / Docker image `katalonstudio/katalon`; plugin **Execute Katalon Studio Tests** downloads or uses preinstalled KRE.

**Evidence:** [Jenkins overview](https://docs.katalon.com/katalon-studio/integrations/cicd-integrations/jenkins-integration/jenkins-integration-overview)

### Other CI

Documented: GitHub Actions, Azure DevOps, GitLab, Bamboo, Docker, CircleCI agents on platform (legacy TestOps doc).

**Evidence:** [Integrations](https://docs.katalon.com/katalon-studio/integrations/integrations-in-katalon-platform), [TestOps execution (legacy)](https://docs.katalon.com/katalon-platform/execute/test-execution-with-testops)

---

# R. Version control / collaboration

### Git in Studio (EGit)

**What it does**  
Clone, commit, push, pull, branches inside IDE.

**Artifacts to commit (documented)**  
`Test Cases/`, `Test Suites/`, `Object Repository/`, `Keywords/`, `Profiles/`, `Data Files/` as needed.

**Collaboration**  
Submodules for shared keyword packages; True Platform Git sync for test management views.

**Evidence:** [Git integration](https://docs.katalon.com/katalon-studio/manage-projects/project-settings/git-integration/git-integration-in-katalon-studio), [Work with Git](https://docs.katalon.com/katalon-studio/manage-projects/project-settings/git-integration/work-with-git-in-katalon-studio)

---

# S. Extensibility

### Custom keywords

Groovy/Java `@Keyword` methods; categories via `keywordObject`.

### Plugins (Katalon Store)

Online install → Reload Plugins; toolbar integrations; offline/private plugins (Enterprise per feature matrix).

### Plugin settings API

`katalon-plugin.json` defines Project Settings pages and keyword classes.

**Evidence:** [Katalon Store install](https://docs.katalon.com/katalon-studio/katalon-store/install-plugins-online-from-katalon-store), [Plugin settings](https://docs.katalon.com/katalon-studio/keywords/custom-keywords/build-custom-keywords-with-settings-in-katalon-studio)

### Test listeners / hooks

Test listeners and Cucumber hooks; setup/teardown at suite/case level.

**Evidence:** [Create test case overview](https://docs.katalon.com/katalon-studio/create-test-cases/create-test-case-overview)

---

# T. Import / export

| Import | Output |
|--------|--------|
| Selenium IDE `.side` | Test cases + suites under imported folders | [Import Selenium IDE](https://docs.katalon.com/katalon-studio/get-started/migrate-from-other-tools/import-selenium-ide-version-3-projects-to-katalon-studio) |
| Selenium/TestNG/JUnit sources | Groovy scripts under `Include/scripts/groovy` | [Selenium migration](https://docs.katalon.com/katalon-studio/get-started/migrate-from-other-tools/seleniumtestngjunit-migration-to-katalon-studio) |
| Postman / OpenAPI / WSDL / SoapUI | WS objects / collections | [Integrations table](https://docs.katalon.com/katalon-studio/integrations/integrations-in-katalon-platform) |
| Export reports | HTML, CSV, PDF, JUnit; TSC export HTML manual | [View suite reports](https://docs.katalon.com/katalon-studio/test-reports/view-test-reports/view-test-suite-and-test-suite-collection-reports-in-katalon-studio) |

---

# U. Integrations (by purpose)

| Purpose | Mechanism (documented) |
|---------|------------------------|
| ALM / TM | Jira, Xray, qTest, TestRail, Azure DevOps Test Plans, Rally, TestLink — platform + Store plugins |
| CI/CD | Jenkins plugin, Docker, CLI, GitHub Actions, etc. |
| Device cloud | Sauce Labs plugin; Test Execution – Cloud |
| Notifications | Email, Slack, Teams (About KS) |
| Defect tracking | Jira integration |

**Evidence:** [Integrations in True Platform](https://docs.katalon.com/katalon-studio/integrations/integrations-in-katalon-platform), [About KS](https://docs.katalon.com/katalon-studio/about-katalon-studio)

---

# V. Failure handling & maintenance

### Failure handling

**What it does**  
Controls whether execution stops, continues with Failed, or continues with Warning on errors.

**How Katalon provides it**  
Project default for **new** steps; per-step override in Manual view; `FailureHandling.*` last parameter in scripts.

**Evidence:** [Configure failure handling](https://docs.katalon.com/katalon-studio/maintain-tests/configure-failure-handling-settings-in-katalon-studio)

---

### Unused object refactor

Reports objects only referenced via `findTestObject("id")`; delete or export.

**Evidence:** [Refactor test objects](https://docs.katalon.com/katalon-studio/maintain-tests/refactor-test-objects-in-katalon-studio)

---

# W. Licensing / tier gates (feature availability)

Many advanced items (Native Windows Recorder, image mobile testing, dual debugger, AI features, TestCloud, etc.) are tied to **Katalon Studio Enterprise** and **Runtime Engine** licenses in the official comparison table.

**Evidence:** [KS Free vs Paid](https://docs.katalon.com/katalon-studio/katalon-studio-enterprise-and-katalon-runtime-engine-license/katalon-studio-vs-katalon-studio-enterprise-features)

---

## Appendix: Features explicitly called out in “Explore Katalon Studio”

- Combined AUT types in one project flow  
- Manual ↔ Script interchangeable editors  
- Self-healing, Smart Wait, Time Capsule  
- Data-driven, BDD, keyword-driven  
- Test suites & collections  
- Test Execution – Cloud  
- True Platform analytics, Jira, Slack/Teams, KRE for CI/CD  

**Evidence:** [Explore Katalon Studio](https://docs.katalon.com/katalon-studio/get-started/explore-katalon-studio)

---

*Document generated for comparative R&D (e.g. Tosca, Playwright). Re-verify version-specific behavior against current release notes before contractual or procurement decisions.*
