# SmartBear TestComplete — Feature R&D Map

**Research question:** What features does TestComplete provide, and in what way does TestComplete provide / implement each feature?

**Primary source:** [TestComplete Documentation](https://support.smartbear.com/testcomplete/docs/) (SmartBear). External sources are labeled **External source**.

**Scope:** Desktop, web, mobile (Appium device cloud + legacy local), checkpoints, Name Mapping, execution (TestComplete / TestExecute), Intelligent Quality (OCR, self-healing, Vision AI). Not marketing copy or a flat checklist.

---

## 1. Research Scope

TestComplete is a Windows-centric IDE for building **Project Suites** containing **Projects** with **Keyword Tests**, **Script units**, **Name Mapping**, **Stores** (baselines), and an **Execution Plan** of **Test Items**. Validation is expressed primarily through **checkpoints** and programmatic checks. **TestExecute** runs the same project artifacts without the full IDE.

Major add-on: **Intelligent Quality** (licensed separately) — cloud OCR (Google Vision via SmartBear service), **self-healing** / **Intelligent Fix**, and **Vision AI** (visual object detection). Classic automation uses property-based identification, MSAA, UI Automation, and web plugins (IE/Edge/Chrome/Firefox/CEF).

---

## 2. TestComplete Automation Model

```text
Project Suite
  → Project(s) [Desktop / Web / Mobile modules + plugins]
       → Tested Apps, Name Mapping (Mapped Objects + Aliases)
       → Keyword Tests | Script routines | Low-level procedures | (ReadyAPI/Selenium items in plan)
       → Stores (Regions, XML, DBTables, Files, …)
       → Execution Plan: Test Items (order, groups, parallel cross-platform groups, parameters, On Error)
  → Run (TestComplete or TestExecute, local / CI / deprecated Network Suite)
       → Log (screenshots, checkpoint details, self-heal / recognition hints)
```

**Aliases:** Short names tests use (`Aliases.app.form.button`); map to **Mapped Objects** with identification criteria ([Name Mapping](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/name-mapping/index.html)).

**Evidence:** [Tests, Test Items, and Test Cases](https://support.smartbear.com/TestComplete/docs/working-with/managing-projects/test-items.html), [Project Suites](https://support.smartbear.com/testcomplete/docs/ver-15-77/working-with/managing-projects/project-suites.html).

---

## 3. Feature Map

### Test authoring

| Approach | Start | Artifact | Edit | Execute |
|----------|-------|----------|------|---------|
| Keyword Test | New Keyword Test / record | `.tcKDT` operations | Keyword Test editor | Test Item, Run from explorer, nested keyword, `KeywordTests.*.Run()` |
| Script Test | New script unit / record script | Script module in project language | Code Editor | Test Item, direct run, called from keyword |
| Record & Playback | Record Keyword/Script/Low-level | New or appended operations | Same editors | As above |
| Manual mapping/checkpoints | Object Spy / wizards | Name Mapping + Stores entries | Mapping editor, checkpoint wizards | Via tests referencing aliases |

Recording is **object-oriented** (clicks/keys on controls), not raw mouse paths by default; can switch to keyword/script/low-level mid-recording ([Recording](https://support.smartbear.com/testcomplete/docs/testing-with/creating/recording/index.html)).

---

### Keyword Tests

**What they do:** Table of **operations** (actions, checkpoints, loops, data-driven loops, run script/keyword, etc.) with parameters.

**How TestComplete provides it:** Drag/drop or record operations; objects picked via aliases or selectors; **Data-Driven Loop** wraps operations over DB Table/Table variables ([Creating Data-Driven Loops](https://support.smartbear.com/TestComplete/docs/keyword-testing/basic/data-driven-loops.html)).

**Workflow:**
```text
Create Keyword Test → add/record operations → bind parameters/variables
→ Execution Plan Test Item → run → operations logged pass/fail
```

---

### Script Tests

**Languages (current):** **JavaScript** (V8 5.8) and **Python** (3.13.3 per current specifics page; verify minor version in your install) recommended; **VBScript** supported; **JScript, DelphiScript, C#Script, C++Script** legacy (add via Create Project dialog, not default wizard) ([Selecting Scripting Language](https://support.smartbear.com/testcomplete/docs/scripting/selecting-the-scripting-language.html)).

**How scripts interact with apps:** Built-in objects (`Sys`, `Aliases`, `KeywordTests`, `aqObject`, `OCR`, `DDT`, `aqHttp`, …), test objects for controls, COM objects ([Scripting overview](https://support.smartbear.com/testcomplete/docs/scripting/overview.html)).

---

### Record & Playback

Captures desktop, web, mobile (per module); auto-updates Name Mapping; checkpoints can be created during recording ([Recording Specifics](https://support.smartbear.com/testcomplete/docs/testing-with/creating/recording/specifics.html)). Modes: keyword, script, **low-level** (desktop/web coordinate events).

---

### Object Spy / Object Browser

| Tool | Role |
|------|------|
| **Object Browser** | Live hierarchy of processes/controls; properties, methods, fields, events; add to Name Mapping, screenshots ([Object Browser](https://support.smartbear.com/testcomplete/docs/testing-with/exploring-apps/object-browser/index.html)) |
| **Object Spy** | Popup picker without tree; same member views; **Highlight in Object Tree**; add/check Name Mapping ([Object Spy](https://support.smartbear.com/testcomplete/docs/testing-with/exploring-apps/object-spy/index.html)) |

**Flow:** On-screen object → properties/methods exposed → optional **Map** → alias used in tests.

---

### Name Mapping

**What it does:** Separates **object identity** from test steps.

**Repository stores per object:** alias, hierarchy position, **identification criteria**, optional **image** ([Name Mapping](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/name-mapping/index.html)).

**Criteria types:**
- Property–value pairs (desktop; local mobile)
- **Selectors:** XPath/CSS (web); identifier/class/XPath (mobile cloud)
- Conditional expressions
- **Extended Find** (search down hierarchy; breadth-first; not for selector-mapped objects)
- Required child objects

Recording auto-maps new objects; runtime resolves via repository. Stale criteria → failure unless healing/recovery applies ([Basic Mapping Criteria](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/name-mapping/basic-mapping-criteria.html)).

---

### Object identification (classic)

**Engines (documented):** Win32/control-specific plugins, **WPF** (`ClrFullClassName`), **MSAA** (`IAccessible`), **UI Automation** providers, web DOM/tree models, **CEF/Electron** (Chrome DevTools Protocol or legacy injection) ([Object Identification](https://support.smartbear.com/testcomplete/docs/ver-15-83/testing-with/object-identification/index.html), [CEF](https://support.smartbear.com/TestComplete/docs/app-testing/web/cef/about.html)).

**Direct addressing (no mapping):** Full name / `Find` / `FindChild` / XPath / `QuerySelector` in scripts — **no self-heal** when used instead of aliases ([Self-Healing Tests](https://support.smartbear.com/testcomplete/docs/testing-with/running/self-healing-tests.html)).

---

### Self-healing / recovery (AI-assisted)

**Classic (non-AI):** **Object recognition hints** — partial sibling matches; log suggests Update/Delete property links ([Recognition Hints](https://support.smartbear.com/testcomplete/docs/testing-with/running/handling-errors/object-not-found/recognition-hints.html)).

**Intelligent Fix (IQ add-on):** On missing mapped object → search same hierarchy level (web: deeper), same class/type, pick closest by **stored screenshot** → continue or fail; post-run suggest criteria update ([Intelligent Fix](https://support.smartbear.com/testcomplete/docs/ver-15-79/testing-with/object-identification/name-mapping/how-to/update/intelligent-fix.html)).

**Self-healing mode:** When enabled (`Tools > Options > Engines > Name Mapping`, or `/SelfHealing` CLI), uses replacement object during run if image stored in mapping; requires **Name Mapping** + **IQ license** + OCR & Self-Healing plugins ([Self-Healing Tests](https://support.smartbear.com/testcomplete/docs/testing-with/running/self-healing-tests.html)).

**Limits:** Not for XPath/CSS-only web locators; not for full-name/`Find`-only tests; images required in repository for auto-continue.

**Vision AI (separate):** Visual objects identified by appearance via SmartBear-hosted AI; self-heal prompts **Confirm object update** (position/icon) ([Vision AI](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/vision-ai/vision.html)).

---

### Visual / image / OCR

| Capability | Input | Processing | Output |
|------------|-------|------------|--------|
| **Region checkpoint** | Window/control/region bitmap | Pixel compare vs **Stores > Regions** | Pass/fail + diff in log |
| **Image compare/find** | Pictures, Regions API | Pixel/compare/find methods | Boolean / coordinates |
| **OCR** (IQ) | Screen object, `Picture`, desktop | Image → `ocr.api.dev.smartbear.com` → **Google Vision API** | Text; checkpoints/actions |
| **OCR checkpoint** | UI element / region | OCR + wildcard pattern match | Log pass/fail |
| **Vision AI** | Screenshots + UI metadata to SmartBear AI | Visual detection / interaction | Visual objects; heal prompts |

OCR is **explicitly OCR** in docs (not inferred). Legacy pre-12.60 OCR modules differ ([OCR](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/ocr/index.html)). Coordinate **low-level** mode is separate from OCR ([Recording](https://support.smartbear.com/testcomplete/docs/testing-with/creating/recording/index.html)).

---

### Checkpoints (validation)

| Type | Inspects | Baseline | Comparison |
|------|----------|----------|------------|
| Property | Object property | Expected value / parameter | `aqObject.CheckProperty` conditions |
| Region | Visual area | Stores/Regions image | Pixel-by-pixel (+ tolerance) |
| OCR | Rendered text | Pattern with wildcards | OCR text match |
| Database | Table/view/query | Stores/DBTables | Row/key column match |
| XML | XML document | Stores/XML | Hierarchy/value check |
| File | File contents | Stores/Files | File comparison |
| Table | Grid control data | Stores/Tables | Tabular compare |
| Web service (SOAP) | Method result | Script/XML checkpoint | Obsolete path; use ReadyAPI for new work |

Overview: [Stores](https://support.smartbear.com/TestComplete/docs/testing-with/checkpoints/stores/index.html), [Property checkpoints](https://support.smartbear.com/TestComplete/docs/testing-with/checkpoints/property/about.html).

---

### Web

**Browsers (documented):** Edge 83–153 (Chromium), Chrome 153, Firefox 91–115.10 ESR / 94–149, IE 11; CEF/Electron; cross-browser record once/run others ([Supported browsers](https://support.smartbear.com/testcomplete/docs/app-testing/web/general/supported-browsers-and-technologies.html)). Plugins: Web Testing, Firefox Support, Chrome Support, CEF ([Requirements](https://support.smartbear.com/testcomplete/docs/app-testing/web/general/requirements.html)).

**Identification:** Name Mapping selectors (XPath/CSS); `Page.contentDocument`, `contentText` for cross-browser ([Cross-browser](https://support.smartbear.com/testcomplete/docs/app-testing/web/general/cross-browser/about.html)).

**Headless:** Not native GUI playback; **WebDriver** + drivers (Chrome/Firefox/Edge) with IQ Headless plugin ([Headless](https://support.smartbear.com/testcomplete/docs/app-testing/web/supported-browsers/headless.html)).

**Training:** Chrome default for local record/run; configure browser paths per docs.

---

### Desktop / Windows

Win32, WPF, MSAA, UIA, Java desktop (per desktop module docs), .NET WinForms, etc. WPF uses `ClrFullClassName` and control-specific methods ([WPF controls](https://support.smartbear.com/testcomplete/docs/app-testing/desktop/wpf/control-support.html)). **Tested Applications** project item launches/configures apps.

---

### Mobile

**Current (recommended):** **Mobile device cloud** — devices managed by **Appium 2.x** (not 3.x), UIAutomator2 (Android), XCUITest (iOS); BitBar or private Appium; `Mobile.ConnectDevice` with capabilities; object mapping via **selectors** ([Mobile requirements](https://support.smartbear.com/TestComplete/docs/app-testing/mobile/device-cloud/requirements.html), [Set up Appium](https://support.smartbear.com/testcomplete/docs/ver-15-81/app-testing/mobile/device-cloud/configure-appium/index.html)).

**Legacy:** Local USB devices (ADB / iTunes); image-based tests when app not preparable (Android); stricter iOS limits ([Testing Mobile Applications](https://support.smartbear.com/testcomplete/docs/app-testing/mobile/index.html)).

**Mobile web:** Documented under web/mobile modules (browser on device), distinct from native app automation.

---

### API / web services

| Mechanism | Status |
|-----------|--------|
| **Web Service** project items (SOAP/WSDL) | **Obsolete**; manual keyword/script calls via `WebServices` object ([About Web Services](https://support.smartbear.com/testcomplete/docs/ver-15-72/app-testing/web/services/index.html)) |
| **REST/HTTP in tests** | `aqHttp` / `aqHttpRequest` in scripts (not a dedicated REST editor) ([Sending HTTP requests](https://support.smartbear.com/testcomplete/docs/ver-15-71/testing-with/advanced/sending-receiving-requests/in-script-tests.html)) |
| **ReadyAPI tests** | Runnable as **Test Items** in Execution Plan ([Test Item List](https://support.smartbear.com/testcomplete/docs/working-with/managing-projects/execution-plan/test-item-list.html)) |

SmartBear directs new API work to **ReadyAPI** (**External product**, documented from TestComplete).

---

### Database / file / data

- **Database checkpoints** + **DBTables** stores; SQL designer in wizard ([Creating Database Checkpoints](https://support.smartbear.com/testcomplete/docs/testing-with/checkpoints/database/creating.html)).
- **DDT drivers:** `DDT.ExcelDriver`, `DDT.CSVDriver`, `DDT.ADODriver` ([DDT Drivers](https://support.smartbear.com/testcomplete/docs/testing-with/data-driven/drivers.html)).
- **Stores/Files** for file baselines.

---

### Data-driven testing

**Keyword:** Data-Driven Loop → DB Table variable (Excel, CSV, DB table/query) or Table variable; bind operation parameters to columns ([Data-Driven Loop operation](https://support.smartbear.com/testcomplete/docs/keyword-testing/reference/data/data-driven-loop.html)).

**Script:** Loop with `DDT.*` drivers or `DriveMethod`.

Separate from **project / project suite variables** ([Project suite variables](https://support.smartbear.com/testcomplete/docs/ver-15-77/working-with/managing-projects/project-suites.html)).

---

### Variables / Stores

- **Project variables**, **project suite variables**, keyword test variables, script variables, routine parameters.
- **Stores** collections hold baseline artifacts for checkpoints (Regions, XML, DBTables, Files, Objects, Tables, WebTesting) ([Stores](https://support.smartbear.com/TestComplete/docs/testing-with/checkpoints/stores/index.html)).
- **Name Mapping** is not a variable store; **Aliases** are the runtime façade.

---

### Events / application control

**Tested Apps** for launch parameters; project **Events** (`OnStartTest`, `OnStopTest`, `OnTimeout`, …) for suite/project lifecycle (see Project Properties / Events in docs). Script: `Runner.Stop` / `Runner.Halt`.

---

### Synchronization

- **Playback > Auto-wait timeout** (default referenced in checkpoint/wait docs).
- **`WaitAliasChild`**, **`WaitProperty`**, **`WaitChild`**, `Find*` methods ([WaitAliasChild](https://support.smartbear.com/testcomplete/docs/reference/test-objects/members/common-for-all/waitaliaschild-method.html)).
- Region checkpoints can poll until match within auto-wait ([Region checkpoint](https://support.smartbear.com/testcomplete/docs/keyword-testing/reference/checkpoints/region.html)).
- Not a single “implicit wait” product term — explicit waits + project timeouts.

---

### Execution

**Test Items:** Keyword tests, script routines, BDD scenarios, low-level procedures, **network suite** (deprecated), unit/Selenium tests, **ReadyAPI** tests, tags ([Execution Plan](https://support.smartbear.com/testcomplete/docs/working-with/managing-projects/execution-plan/test-item-list.html)).

**Project suite:** Ordered enabled projects with stop-on-error, per-project timeout ([Test Items page](https://support.smartbear.com/testcomplete/docs/working-with/managing-projects/project-suite-editor/test-items-page.html)).

**Parallel:** Execution Plan **parallel groups** for cross-platform web/mobile device cloud environments (Environments column).

---

### Distributed / parallel

**Network Suite (deprecated):** Master/slave projects, jobs/tasks (tasks in job run concurrently), synchpoints, NetSuite variables ([Basic Concepts](https://support.smartbear.com/testcomplete/docs/ver-15-76/testing-with/deprecated/distributed/basic-concepts.html)). SmartBear recommends **CI/CD** instead.

**Parallel ≠ distributed:** CI parallel agents or Execution Plan parallel groups vs Network Suite remote hosts.

---

### Debugging

Keyword: step-through, breakpoints on operations; Script: full debugger (breakpoints, watch, step). **Pause** on indicator / Shift+F3 ([Running Tests](https://support.smartbear.com/testcomplete/docs/testing-with/running/index.html)). Logs + checkpoint details + pictures on failure.

---

### Reporting

**Test Log** (MHT/XML export options in docs), checkpoint messages, screenshots (Picture panel), self-healing / Intelligent Fix / recognition hint entries. Integrations: Zephyr, Azure DevOps test case binding on test items ([Execution Plan Properties](https://support.smartbear.com/testcomplete/docs/working-with/managing-projects/execution-plan/properties.html)).

---

### TestExecute

Lightweight runner; **create/edit only in TestComplete**; runs same projects; supports Desktop, Web, Mobile, **Intelligent Quality**; separate floating license; version should match authoring TestComplete ([About TestExecute](https://support.smartbear.com/testcomplete/docs/other-tools/testexecute.html), [TestExecute docs](https://support.smartbear.com/testexecute/docs/)). **No CrossBrowserTesting** runs from TestExecute (per command-line docs). **No** cloud BitBar-only features unless same plugins licensed.

---

### CI/CD

**TestComplete.exe** / **TestExecute** command line: `/run` project suite, `/SelfHealing`, exit codes for CI (see [Command Line](https://support.smartbear.com/testcomplete/docs/working-with/automating/command-line-and-exit-codes/command-line.html) — use current version path). Jenkins/Azure DevOps/GitHub Actions referenced in SmartBear ecosystem docs; pattern: agent with TestExecute → run suite → publish log.

---

### Collaboration / version control

**Git plugin** (git.exe), commit/push/pull from IDE; optional **TortoiseGit**; TFVC plugin for DBTable elements ([Git integration](https://support.smartbear.com/testcomplete/docs/working-with/integration/scc/git/index.html)). Projects are file-based (.pjs, .tc*, NameMapping, Stores on disk).

---

### Extensibility

**Script extensions**, custom checkpoints, plugins via **Install Extensions**, user-defined objects; ReadyAPI/Selenium test item types ([Stores.Create](https://support.smartbear.com/testcomplete/docs/working-with/extending/script/objects-reference/stores/create.html)).

---

### AI features (documented)

| Feature | User input | Processing (per docs) | Output |
|---------|------------|----------------------|--------|
| Self-healing / Intelligent Fix | Mapped alias fails | Hierarchy/class search + screenshot similarity | Replacement object; suggested mapping update |
| OCR | Image/screen area | SmartBear OCR service → Google Vision | Text; checkpoints |
| Vision AI | Enable Visual Object Detection | Screenshots + metadata to SmartBear AI endpoint | Visual objects; confirm updates |

**Requires:** Intelligent Quality add-on license, plugins, network to SmartBear endpoints ([OCR requirements](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/ocr/requirements.html)). Marketing claims on success rates on [smartbear.com Intelligent Quality](https://smartbear.com/product/testcomplete/features/intelligent-quality/) are **External source**.

**Not verified in help:** Natural-language test generation, GenAI script authoring as a first-class TestComplete feature (2026 help index searched via research pass).

---

### Classic vs AI (summary)

| Classic | AI-assisted (IQ / Vision) |
|---------|---------------------------|
| Property/selector Name Mapping | Screenshot-based Intelligent Fix |
| Extended Find | Vision AI visual objects |
| Recognition hints (siblings) | Self-healing continue + post-run fix |
| Region pixel checkpoints | OCR text recognition |

---

## 4. Capability → How TestComplete Provides It

| Capability | What it does | How provided | Artifact | Platform | Underlying (if documented) | Evidence |
|------------|--------------|--------------|----------|----------|----------------------------|----------|
| Keyword automation | User-flow steps | Operations + aliases | Keyword test | All modules | TC test engine | [Keyword creating](https://support.smartbear.com/TestComplete/docs/keyword-testing/creating-recording.html) |
| Script automation | Programmatic tests | JS/Python units | Script | All | V8 / Python | [Scripting languages](https://support.smartbear.com/testcomplete/docs/scripting/selecting-the-scripting-language.html) |
| Object repository | Stable object refs | Name Mapping + Aliases | `.tcNM` mapping | Web/desktop/mobile | MSAA/UIA/ DOM/Appium per app type | [Name Mapping](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/name-mapping/index.html) |
| Self-heal | Survive property changes | Intelligent Fix + optional heal mode | Updated mapping (manual confirm) | Mapped objects w/ images | IQ + screenshot compare (algorithm unspecified) | [Self-Healing](https://support.smartbear.com/testcomplete/docs/testing-with/running/self-healing-tests.html) |
| Visual verify | UI appearance | Region checkpoint | Stores/Regions | Desktop/web | Pixel compare | [Region checkpoints](https://support.smartbear.com/TestComplete/docs/testing-with/checkpoints/regions/about.html) |
| OCR verify | Text on screen | OCR checkpoint / `OCR.Recognize` | Log | Desktop/mobile | Google Vision via SmartBear API | [OCR](https://support.smartbear.com/testcomplete/docs/testing-with/object-identification/ocr/index.html) |
| Data-driven | Multi-input runs | Data-Driven Loop / DDT | Variables + external files | All | ADO/Jet for CSV | [Data-driven loops](https://support.smartbear.com/TestComplete/docs/keyword-testing/basic/data-driven-loops.html) |
| Web cross-browser | Same test, many browsers | Browsers collection, parameters | Script/keyword | Web | Browser-specific plugins + WebDriver headless | [Cross-browser](https://support.smartbear.com/testcomplete/docs/app-testing/web/general/cross-browser/about.html) |
| Mobile native | App UI | Appium session + selectors | Name Mapping | Mobile cloud | **Appium 2.x**, UIAutomator2/XCUITest | [Mobile requirements](https://support.smartbear.com/TestComplete/docs/app-testing/mobile/device-cloud/requirements.html) |
| Unattended run | CI execution | TestExecute CLI | Same project files | Windows agents | TestExecute runtime | [TestExecute](https://support.smartbear.com/testexecute/docs/) |
| SOAP API | Service call | Web Service items | WS definitions | Web | .NET WCF or native API | [Web services obsolete](https://support.smartbear.com/testcomplete/docs/app-testing/web/services/creating.html) |
| REST HTTP | HTTP call | `aqHttp` in scripts | Script only | Any | WinHTTP-style stack (IWinHttpRequest analog) | [aqHttp](https://support.smartbear.com/testcomplete/docs/reference/program-objects/aqhttp/createrequest.html) |

---

## 5. Key Architectural Observations

1. **Name Mapping is the hub** for maintainable UI tests; aliases decouple steps from volatile property sets or selectors.
2. **Keyword and script tests interoperate** (`Run Keyword Test`, `KeywordTests.Run`, record-switch-insert-call).
3. **Checkpoints + Stores** are the primary validation model (not a separate assertion DSL).
4. **Self-healing is conditional** — IQ add-on, images in mapping, property-based mapped web objects; XPath/CSS locator tests exclude healing.
5. **OCR and Vision AI are cloud-dependent** SmartBear services; distinct from legacy on-machine OCR.
6. **Mobile** is explicitly **Appium-backed** for current device-cloud path; legacy local path is separate.
7. **API:** SOAP support obsolete inside TC; REST via scripting; ReadyAPI for full API product.
8. **Scale-out:** Deprecated Network Suite; parallel via CI or Execution Plan device-cloud groups.
9. **Authoring vs run:** TestComplete IDE vs TestExecute runner licenses and capabilities differ (e.g. CBT).
10. **Desktop identification** layers: native control support → MSAA → UIA → mapping criteria.

---

## 6. Research Gaps

| Topic | Status |
|-------|--------|
| Exact self-heal / Intelligent Fix matching algorithm | Not specified in public documentation |
| Vision AI model architecture | Not specified; hosted endpoint only |
| Playwright integration depth | Selenium/unit test items in Execution Plan; no native Playwright engine documented |
| Native macOS/Linux TestComplete IDE | Docs assume Windows IDE/workstation |
| AI test generation in TestComplete | Not found in official help during this pass |
| Community-only tuning (e.g. WaitProperty poll interval) | **External source**; not in official docs |
| Current patch browser versions beyond doc revision dates | Check [Supported browsers](https://support.smartbear.com/testcomplete/docs/app-testing/web/general/supported-browsers-and-technologies.html) for your TC version |

---

*Second-pass searches: Keyword/Script/Record, Name Mapping, Object Spy, self-healing, Vision AI, OCR, checkpoints, web/desktop/mobile, data-driven, TestExecute, distributed/Network Suite, CI command line, Git, Intelligent Quality, web services, aqHttp, Stores, Execution Plan.*
