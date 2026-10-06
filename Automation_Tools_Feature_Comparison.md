# Automation Tools — Feature Comparison

**Question:** What features do these tools provide, and how does each tool provide that feature?

**Sources:** Workspace R&D docs — [Katalon](katalon-feature-mapping-research.md), [Tosca](tricentis-tosca-feature-mapping-research.md), [mabl](mabl-feature-rnd.md), [TestComplete](testcomplete-feature-rnd.md); [Playwright docs](https://playwright.dev/docs/intro).

## Comparison Legend

| Symbol | Meaning |
|--------|---------|
| ✅ | Native / documented support |
| ⚠️ | Partial, indirect, integration, or limited scope |
| ❌ | Not provided natively |

Cells: **status + short how** (≈5–15 words).

---

## 1. Test Creation & Authoring

| Feature | Katalon | Tosca | mabl | TestComplete | Playwright |
|---------|---------|-------|------|--------------|------------|
| Manual / visual authoring | ✅ Manual keyword rows | ✅ TestSteps + TestStepValues | ✅ Trainer step editor | ✅ Keyword Test operations | ❌ Code-first IDE optional |
| Script-based authoring | ✅ Groovy/Java Script view | ⚠️ TBox scripts / expert modules | ⚠️ JavaScript snippets in steps | ✅ JS/Python script units | ✅ TS/JS/Python/Java/.NET tests |
| Keyword / model authoring | ✅ Built-in keywords | ✅ Module-driven TestSteps | ⚠️ Low-code steps, not keywords | ✅ Keyword Tests | ❌ |
| Model-based design | ⚠️ Object repo, not MBT | ✅ TestCase-Design sheets | ❌ | ❌ | ❌ |
| BDD / Gherkin | ✅ Cucumber in Studio | ⚠️ Integrations / custom | ❌ | ❌ | ⚠️ Community libs (e.g. playwright-bdd) |
| Reusable components | ✅ Call Test Case, custom keywords | ✅ Modules reused by TestSteps | ✅ Flows | ✅ Flows, Run Keyword Test | ✅ Fixtures, `import`, helpers |
| Test templates | ⚠️ Project templates | ✅ Module/TestCase templates | ⚠️ Agent outlines | ⚠️ Sample projects | ✅ `init` project scaffold |
| Natural-language → tests | ✅ AI Assistant / prompts | ⚠️ Cloud ARA (record assist) | ✅ mabl agent prompts | ❌ Not in help | ❌ |
| AI test generation | ✅ Studio Assist, TrueTest | ⚠️ ARA, Cloud inventory | ✅ Agentic browser/mobile/API | ❌ Not in help | ❌ |
| Step editing after create | ✅ Manual/Script/Recorder edit | ✅ TestStepValues grid | ✅ Trainer bulk edit | ✅ Keyword/script editors | ✅ Edit generated code |

---

## 2. Recording & Object Identification

| Feature | Katalon | Tosca | mabl | TestComplete | Playwright |
|---------|---------|-------|------|--------------|------------|
| Test recording / generation | ✅ Web/Mobile/Windows recorders | ✅ XScan / ARA capture | ✅ Trainer Rec | ✅ Record → Keyword/Script | ✅ Codegen → test code |
| Web interaction capture | ✅ Recorder + Spy | ✅ XBrowser XScan | ✅ Trainer browser | ✅ Record web actions | ✅ Codegen records actions |
| Desktop capture | ✅ Windows/FlaUI recorders | ✅ UIA/Java/WPF scan | ❌ | ✅ Record desktop | ❌ |
| Mobile native capture | ✅ Mobile Recorder (Appium) | ✅ Mobile Engine 3.0 scan | ✅ Mobile Trainer | ✅ Record on Appium devices | ❌ |
| Object repository model | ✅ Object Repository | ✅ Modules + attributes | ✅ Element history (implicit) | ✅ Name Mapping | ❌ Locators in code |
| Object spy / picker | ✅ Web/Mobile Spy | ✅ XScan pick controls | ✅ Trainer pick (live app) | ✅ Object Spy / Browser | ✅ Codegen pick locator |
| Aliases / friendly names | ✅ Test objects in repo | ✅ Business parameters / names | ✅ Mapped step targets | ✅ Aliases tree | ❌ Variable names in code |
| Locator strategies | ✅ XPath, CSS, attrs, image, Smart | ✅ Technical Module properties | ✅ CSS/XPath/Playwright syntax | ✅ Properties, XPath/CSS | ✅ Role, text, test id, CSS, XPath |
| Dynamic / runtime find | ✅ `findTestObject`, variables | ✅ Buffers, `{CP}`, expressions | ✅ Configure Find, custom find | ✅ Find/WaitAliasChild methods | ✅ Locator re-resolve each action |
| Extended / deep search | ⚠️ Smart Locator heuristics | ✅ Engine search settings | ✅ Extended find (mapping) | ✅ Extended Find | ⚠️ Scoped locators only |
| Shadow DOM | ✅ Documented web support | ⚠️ Engine-dependent | ✅ Open shadow documented | ✅ Open shadow only | ✅ Piercing via locators |
| MSAA / UIA desktop | ✅ Windows spy paths | ✅ UIA engines | ❌ | ✅ MSAA + UI Automation | ❌ |

---

## 3. Self-Healing / Synchronization / Visual / AI

| Feature | Katalon | Tosca | mabl | TestComplete | Playwright |
|---------|---------|-------|------|--------------|------------|
| Self-healing / auto-heal | ✅ Classic multi-locator + AI heal | ✅ Self-healing Mode (Engines 3.0) | ✅ Standard + advanced auto-heal | ✅ IQ Self-healing + Intelligent Fix | ❌ Not self-healing |
| AI object recovery | ✅ AI self-healing (LLM) | ✅ Vision AI steer/scan | ✅ Advanced auto-heal (GenAI) | ✅ Screenshot similarity (IQ) | ❌ |
| Fallback locator / heal | ✅ Alternate locators in repo | ✅ Recovery + self-healing | ✅ Visual fallback on custom find | ✅ Recognition hints (siblings) | ⚠️ Strict locator retry only |
| Smart / intelligent wait | ✅ Smart Wait (BiDi/extension) | ✅ Synchronization settings | ✅ Intelligent Wait (learned timing) | ⚠️ Auto-wait timeout + waits | ✅ Auto-wait actionability checks |
| Explicit waits | ✅ `WebUI.delay`, Smart Wait | ✅ Wait attributes / TBox | ✅ Static/wait-until steps | ✅ WaitProperty, WaitAliasChild | ✅ `locator.waitFor`, timeouts |
| Polling / retry on assert | ✅ Smart Wait / flaky handling | ✅ Engine retries | ✅ Intelligent Wait + finds | ⚠️ Region checkpoint polling | ✅ `expect` auto-retry |
| Image-based object ID | ✅ Web/mobile image locators | ✅ IBTA image modules | ✅ Visual find (GenAI description) | ⚠️ Low-level / legacy mobile | ❌ |
| Screenshot comparison | ✅ Platform visual testing | ✅ Vision AI / image areas | ✅ Visual assertions (LLM) | ✅ Region checkpoints | ✅ `toHaveScreenshot`, trace |
| Visual AI assertions | ✅ Platform layout/AI zones | ✅ Vision AI verification | ✅ Visual assertions (screenshot+LLM) | ⚠️ Vision AI objects | ❌ Native visual AI |
| OCR / text from images | ⚠️ Limited / platform | ✅ Tesseract OCR (IBTA) | ❌ Not named OCR in help | ✅ OCR via Google Vision (IQ) | ❌ |
| Coordinate / low-level input | ⚠️ Mobile offset record | ⚠️ Coordinate steering | ❌ | ✅ Low-level recording mode | ⚠️ `mouse` API (not productized) |
| AI test generation | ✅ Assist, TrueTest, API gen | ⚠️ ARA, Cloud | ✅ Agentic creation | ❌ Not verified | ❌ |
| AI debugging / results | ✅ Failure troubleshoot, TestOps | ⚠️ Logs, Cloud | ✅ Conversational results analysis | ⚠️ Intelligent Fix suggestions | ❌ Native AI analysis |
| AI application testing | ⚠️ LLM keywords | ❌ | ✅ Visual assertions on LLM UI | ❌ | ❌ |
| AI agent / MCP integration | ✅ AI Assistant MCP | ❌ | ✅ mabl MCP cloud | ❌ | ❌ |
| AI scripting assist | ✅ Inline Generate/Explain | ❌ | ✅ GenAI script/DB query | ❌ GenAI script (IQ) | ❌ |

---

## 4. Web / Mobile / Desktop / API / Special

| Feature | Katalon | Tosca | mabl | TestComplete | Playwright |
|---------|---------|-------|------|--------------|------------|
| Browser automation engine | ✅ Selenium 4 WebDriver | ✅ XBrowser (Engines 3.0) | ✅ Cloud browsers (Chrome default train) | ✅ Chrome/Firefox/WebKit/Edge plugins | ✅ Playwright Chromium/Firefox/WebKit |
| Cross-browser execution | ✅ Profiles + TestCloud | ✅ ExecutionLists / matrices | ✅ Plan multi-browser multiply runs | ✅ Browsers collection loops | ✅ Projects per browser |
| Headless browsers | ✅ KRE/Cloud | ✅ Engine settings | ✅ Cloud/local headless | ⚠️ WebDriver headless (IQ) | ✅ `headless: true` |
| Browser contexts / isolation | ⚠️ Selenium sessions | ⚠️ Engine instances | ⚠️ Per run environment | ⚠️ Per process/window | ✅ `BrowserContext` per test |
| Network intercept / mock | ⚠️ Limited / proxy | ⚠️ API/OSV patterns | ⚠️ API steps in browser tests | ⚠️ Script-level | ✅ `route`, `fulfill`, HAR |
| Tracing / HAR | ⚠️ Logs, video (platform) | ⚠️ ActualLog detail | ✅ Cloud step diagnostics | ⚠️ Logs, screenshots | ✅ Trace Viewer, HAR |
| Mobile web emulation | ✅ Mobile browser profiles | ⚠️ Mobile web via engines | ✅ Browser test mobile web tab | ⚠️ Web mobile emulation | ✅ `devices[...]` emulation |
| Native Android automation | ✅ Appium (Studio/Cloud) | ✅ Mobile Engine 3.0 | ✅ Cloud/local Appium path | ✅ Appium 2.x device cloud | ❌ |
| Native iOS automation | ✅ Appium XCUITest | ✅ Mobile Engine 3.0 | ✅ iOS cloud builds | ✅ Appium + sim builds | ❌ |
| Real mobile devices | ✅ TestCloud / local Appium | ✅ Device configs | ✅ Cloud + USB Android local | ✅ Appium/BitBar | ❌ |
| Windows desktop apps | ✅ FlaUI / WinAppDriver | ✅ UIA, WinForms, WPF, Java | ❌ | ✅ Win32/WPF/MSAA/UIA | ❌ |
| SAP / enterprise UIs | ⚠️ Plugins | ✅ SAP engines, Salesforce | ❌ | ⚠️ Supported stacks in docs | ❌ |
| REST API authoring | ✅ Web Service requests | ✅ API Engine 3.0 / API Scan | ✅ API Test Editor | ⚠️ `aqHttp` scripts | ✅ `APIRequestContext` |
| SOAP / WSDL | ✅ Import WSDL | ✅ API Scan / engines | ⚠️ Via API steps | ⚠️ Obsolete Web Service items | ❌ Native SOAP product |
| Postman / OpenAPI | ✅ Import OpenAPI/Postman | ✅ API Scan import | ✅ Postman import | ⚠️ Manual REST scripts | ⚠️ Manual / codegen clients |
| API + UI same test | ✅ Same test case | ✅ TestCase mix steps | ✅ API steps in browser tests | ✅ Mixed keyword/script | ✅ `request` fixture + page |
| Database validation | ✅ JDBC keywords | ✅ DB modules / buffers | ✅ DB query steps | ✅ DB checkpoints | ❌ Use libraries in test |
| PDF testing | ⚠️ Limited | ✅ PDF engine modules | ✅ PDF visual / viewer | ✅ PDF download assertions | ⚠️ Download + parse libs |
| Email testing | ⚠️ Third-party / custom | ⚠️ Custom integrations | ✅ mabl Mailbox | ❌ Not highlighted | ❌ |
| File / download checks | ✅ Keywords | ✅ File modules | ✅ Download assertions | ✅ File/region checkpoints | ✅ `download` event, `expect` |
| Accessibility testing | ⚠️ Plugins | ⚠️ Integrations | ✅ axe-core steps (browser) | ✅ axe-core accessibility step | ⚠️ `@axe-core/playwright` |
| Performance / load | ⚠️ Katalon Load (separate) | ⚠️ Neoload ecosystem | ✅ Performance test (reuse func) | ❌ Not load product | ❌ |
| Browser perf metrics | ⚠️ TestOps / reports | ⚠️ Logs | ✅ Coverage performance widgets | ⚠️ From functional runs | ⚠️ Trace network timing |

---

## 5. Data / Execution / Debugging / Reporting / CI/CD / Org

| Feature | Katalon | Tosca | mabl | TestComplete | Playwright |
|---------|---------|-------|------|--------------|------------|
| Data-driven loops | ✅ Test data files, variables | ✅ TDS, TestSheets, instances | ✅ DataTables scenarios | ✅ Data-Driven Loop, DDT | ✅ Parametrize / external data in code |
| Excel / CSV data | ✅ Test data, Excel keywords | ✅ Excel engines | ✅ DataTable import CSV | ✅ Excel/CSV via DDT | ⚠️ Read in test code |
| Environment variables | ✅ Profiles, GlobalVariable | ✅ `{CP}`, environments | ✅ Environment variables | ✅ Env + suite variables | ✅ `process.env`, config |
| Encrypted secrets | ✅ `setEncryptedText` | ✅ Secret management patterns | ✅ Credentials vault types | ✅ Cloud credentials | ⚠️ CI secrets / `.env` |
| Property / text assertions | ✅ Verify keywords | ✅ Verification TestSteps | ✅ Element/variable assertions | ✅ Property checkpoints | ✅ `expect(locator)` matchers |
| Visual / screenshot assert | ✅ Platform visual (cloud) | ✅ Vision AI / images | ✅ Visual assertions | ✅ Region checkpoints | ✅ `toHaveScreenshot` |
| API response assertions | ✅ WS keywords | ✅ API Scan validations | ✅ API editor assertions | ⚠️ Script check | ✅ `expect(response)` |
| DB assertions | ✅ DB keywords | ✅ DB modules | ✅ DB query validate | ✅ Database checkpoints | ❌ |
| Local execution | ✅ Studio, KRE | ✅ Commander ScratchBook/lists | ✅ Desktop App / CLI local | ✅ TestComplete / TestExecute | ✅ `npx playwright test` |
| Cloud execution | ✅ TestCloud, True Platform | ✅ Tosca Cloud playlists | ✅ mabl cloud runs | ❌ IDE local; CBT from TC only | ❌ Bring your own grid |
| Parallel execution | ✅ TSC parallel, KRE | ✅ DEX parallel events | ✅ Plan parallel; CLI sequential local | ⚠️ Execution Plan parallel groups | ✅ Workers, `fullyParallel` |
| Distributed execution | ⚠️ Cloud agents | ✅ Distributed Execution (DEX) | ✅ Cloud scale-out | ⚠️ Network Suite (deprecated) | ⚠️ CI matrix sharding |
| Scheduling | ✅ TestOps schedules | ✅ Execution scheduling | ✅ Plan schedule triggers | ⚠️ External scheduler | ⚠️ CI cron only |
| Headless CI runner | ✅ KRE headless | ✅ CI clients | ✅ CI Runner CLI headless | ✅ TestExecute CLI | ✅ Default in CI |
| Debugger / breakpoints | ✅ Script debug mode | ⚠️ Pause / logs | ⚠️ Trainer replay | ✅ Script/keyword debug | ✅ Inspector, `page.pause` |
| Execution logs | ✅ Studio + TestOps | ✅ ActualLog, reports | ✅ Cloud test output | ✅ Test Log | ✅ HTML report + list reporter |
| Video on failure | ✅ TestOps / cloud | ⚠️ Integrations | ✅ Cloud recordings | ⚠️ Limited | ✅ `video: retain-on-failure` |
| Failure AI analysis | ✅ TestOps AI analysis | ⚠️ Cloud analytics | ✅ Conversational analysis | ⚠️ Intelligent Fix post-run | ❌ |
| Test organization unit | ✅ Test Cases / Suites | ✅ TestCases / ExecutionLists | ✅ Tests / Plans / labels | ✅ Project + Execution Plan | ✅ Spec files + `describe` |
| Suite / plan orchestration | ✅ Test Suite Collection | ✅ ExecutionLists, playlists | ✅ Plans + stages | ✅ Project Suite order | ✅ Projects + dependencies |
| Reuse without duplication | ✅ Object repo + call case | ✅ Module reuse | ✅ Flows | ✅ Flows + mapping | ✅ Imports, fixtures, POM |
| Tags / labels | ✅ Tags (Studio) | ✅ Properties / filtering | ✅ Test labels | ⚠️ Test items by tag | ⚠️ Grep by title/tag in CI |
| Application coverage (non-code) | ⚠️ TestOps coverage | ⚠️ Risk/coverage analytics | ✅ Link crawler + coverage UI | ⚠️ Coverage dashboards | ❌ |
| Reporting dashboards | ✅ Katalon True Platform | ✅ qTest, reports, Cloud | ✅ Coverage, insights | ⚠️ Log + integrations | ✅ HTML report only |
| CI/CD CLI | ✅ KRE CLI | ✅ CI clients, Jenkins API | ✅ mabl CLI | ✅ TC/TestExecute CLI | ✅ `playwright test` |
| REST automation API | ✅ TestOps / platform APIs | ✅ Cloud/Jenkins APIs | ✅ mabl REST + deployments | ⚠️ Limited TC APIs | ❌ Test runner only |
| Git integration | ✅ EGit in Studio | ✅ Multi-user repo / SCC | ⚠️ GitHub integration | ✅ Git plugin | ✅ Tests as source files |
| Plugins / extensions | ✅ Katalon Store | ✅ TBox / custom modules | ⚠️ MCP, integrations | ✅ Install Extensions | ✅ npm packages, fixtures |
| Import Playwright / Selenium | ⚠️ Limited | ❌ | ✅ CLI import Playwright/Selenium | ✅ CLI import Playwright/Selenium | N/A (native) |
| Service virtualization | ❌ | ✅ OSV (separate) | ❌ | ❌ | ❌ |
| Zephyr / ALM sync | ✅ TestOps integrations | ✅ qTest native | ✅ GitHub checks/issues | ⚠️ ADO test case link | ⚠️ Reporters / third-party |

---

## Key Differences

- Tosca centers automation on **Modules and TestSteps** with Engines 3.0 steering, not locator-first scripts.
- Katalon combines **keyword Studio**, **Selenium/Appium/FlaUI engines**, and **True Platform** for cloud, visual, and analytics.
- mabl is **cloud-first**: Trainer authoring, **Name Mapping–like element history**, **Flows/Plans**, and **IQ-style AI** for create/heal/assert.
- TestComplete pairs **Keyword Tests** and **script units** with **Name Mapping**, **Stores/checkpoints**, and **TestExecute** for unattended runs.
- Playwright is a **browser automation library + test runner**: strong **locators, auto-wait, trace, APIRequest**, and **parallel projects**—no native desktop, native mobile app, or enterprise test management.
- **Self-healing** in commercial tools is a **product feature** (mapping/heuristics/AI); Playwright **retries locators** but does not **repair object models**.
- **OCR** is explicit in Tosca (Tesseract) and TestComplete (IQ); mabl uses **visual/GenAI**, not documented as OCR.
- **Native mobile** requires Appium-class stacks (Katalon, mabl, TestComplete, Tosca); Playwright only **emulates mobile browsers**.
- **SOAP API** first-class in Tosca/Katalon; Playwright and mabl favor **REST** patterns.
- **Distributed execution**: Tosca **DEX** and mabl **cloud** vs Playwright **parallel workers** on one machine/CI.

---

## Sources

| Tool | Primary source |
|------|----------------|
| Katalon | [docs.katalon.com](https://docs.katalon.com/) — see `katalon-feature-mapping-research.md` |
| Tricentis Tosca | [docs.tricentis.com](https://docs.tricentis.com/) — see `tricentis-tosca-feature-mapping-research.md` |
| mabl | [help.mabl.com](https://help.mabl.com/), [docs.mabl.com](https://docs.mabl.com/) — see `mabl-feature-rnd.md` |
| TestComplete | [support.smartbear.com/testcomplete/docs](https://support.smartbear.com/testcomplete/docs/) — see `testcomplete-feature-rnd.md` |
| Playwright | [playwright.dev/docs](https://playwright.dev/docs/intro) — codegen, locators, auto-wait, API testing, emulation, trace, parallel |

---

## Research gaps

- Playwright **real-device** mobile browser support varies by vendor; not a first-class Playwright Test feature in core docs.
- Katalon **Load** / Tosca **Neoload** depth not expanded here (separate products).
- mabl **Active Coverage** marketing vs help-only features not fully matrixed.
- TestComplete **AI test generation** not verified in help (marked ❌).
- Cross-tool **version-specific browser builds** (e.g. exact Chrome build) change frequently—verify against each vendor’s supported-versions page before procurement.

**Validation:** Markdown tables render; **feature rows: 92** (excluding header/legend); **5 comparison tables** + legend, key differences, sources.
