# Tricentis Tosca Feature-Mapping Research (Official Documentation)

**Research question:** What features does Tosca provide, and in what way does Tosca provide / implement each feature?

**Primary source:** [Tricentis Documentation](https://docs.tricentis.com/) — predominantly **Tosca 2026.1 LTS** and **Tosca Cloud** topics unless noted.

**Scope:** Tosca Commander (on-prem), Engines 3.0 / XModules, XScan, API Scan, TestCase-Design, execution (ScratchBook, ExecutionLists, Distributed Execution), CI/CD, Vision AI, mobile, integrations (qTest), and related platform services.

**Comparison note:** Tosca is **model- and object-centric** (Modules → TestSteps → TestStepValues), not script-first. This document uses Tosca terminology.

---

## Documented product inventory

| Area | Role (per docs) | Evidence |
|------|-----------------|----------|
| **Tosca Commander** | GUI for planning, creating, configuring, and running tests in a **workspace** | [Get to know your workspace](https://docs.tricentis.com/tosca-2026.1/en-us/content/first_steps/get_to_know_tosca_workspace.htm) |
| **Workspace** | Single-user (local repo) or **multi-user** (common repository, check-in/out) | Same; [Create single-user workspace](https://docs.tricentis.com/tosca-2026.1/en-us/content/installation_tosca/workspaces_create_singleuser.htm) |
| **Modules / XModules** | Reusable technical building blocks; **Engines 3.0** automation | [Create and manage Modules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm) |
| **TestCases** | Ordered **TestSteps** composed from Modules | [Create demo TestCase](https://docs.tricentis.com/tosca-2026.1/en-us/content/tutorial/creating_test_cases.htm) |
| **TestCase-Design** | Model-based test **design** (TestSheets, classes, instances, templates) | [Work with TestCase-Design](https://docs.tricentis.com/tosca-2026.1/en-us/content/testcase_design/testcase_design_intro.htm) |
| **Tosca XScan** | Scan GUIs → Module with control metadata | [Create Modules by scanning](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/scan_modules_overview.htm) |
| **Tosca API Scan** | Scan/test APIs; export to Commander/OSV | [Tricentis Tosca API Scan](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/api_scan.htm) |
| **Tosca Cloud** | Cloud authoring/execution (playlists, agents, Vision AI, ARA) | [Tosca Cloud test case design](https://docs.tricentis.com/tosca-cloud/en-us/content/testcase_design/overview.htm) |
| **Tricentis ARA** | Recording assistant → TestCases/Modules (included Tosca 15.0+) | [ARA recorder menu](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/recorder/recorder_menu.htm) |

---

## Tosca conceptual model (relationship map)

```text
Application controls (AUT)
  → XScan / API Scan / ARA / Standard Modules capture technical representation
  → Module (+ ModuleAttributes)
  → TestCase: TestStep (instance of Module) + TestStepValues (ActionMode + Value per attribute)
  → Optional: Buffers, {CP[TCP]}, text expressions, TestCase-Design-generated instances
  → Execution: ScratchBook (ephemeral) OR ExecutionList → logs (ActualLog, TestCaseLog, …)
  → Optional: TestEvent (Distributed Execution) / qTest TestEvent / CI client / Cloud playlist
```

**Reuse rule (documented):** Editing a **Module** updates all **TestSteps** derived from it; TestStep-only changes do not alter the Module.

**Evidence:** [Modules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm), [Tutorial TestCase](https://docs.tricentis.com/tosca-2026.1/en-us/content/tutorial/creating_test_cases.htm)

---

## Platform / engine relationship (summary)

| Layer | What it is (documented) | Examples |
|-------|-------------------------|----------|
| **Steering / Engines 3.0** | Technology-specific drivers that interpret Module technical properties and perform UI/API actions | XBrowser, UIA, DotNet WinForms/WPF, Java, Mobile Engine 3.0, API Engine 3.0, Excel engines, PDF, Host/PuTTY, Salesforce scan |
| **TBox** | Standard subset modules, ActionModes, buffers, recovery, self-healing behavior | `TBox Set Buffer`, Recovery Scenarios |
| **Commander product** | Workspace UI, object model, execution organization, TCPs, settings hierarchy, integrations | ExecutionLists, ScratchBook, DEX, qTest linking |
| **Cloud product** | SaaS workflows (build inventory, playlists, Jenkins API) | [Scan XScan Cloud](https://docs.tricentis.com/tosca-cloud/en-us/content/create_tests/scan_xscan.htm) |

Tosca states **XModules use Engines 3.0** for test automation.

**Evidence:** [Modules – XModules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm)

---

# A. Workspace, project, and authoring shell

### Tosca Commander workspace

**What it does**  
Hosts all test assets: tabs (Modules, TestCases, Execution, …), project tree, Details (TestStepValues), Properties, settings.

**How Tosca provides it**  
`.tws` workspace files; **single-user** (SQLite/none) vs **multi-user** shared repository with check-in/out coloring in tree.

**Typical workflow**
```text
Start Commander → open/create workspace → select tab (e.g. TestCases)
→ edit objects in tree + Details → check in (multi-user) → execute
```

**Evidence:** [Workspace anatomy](https://docs.tricentis.com/tosca-2026.1/en-us/content/first_steps/get_to_know_tosca_workspace.htm)

---

### Project root element

**What it does**  
Root of repository tree; global project options, user admin (multi-user), jump-to-object by UniqueId/NodePath.

**How Tosca provides it**  
**Home > Project**; properties on root affect all workspaces attached to common repository.

**Evidence:** [Project root element](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/project_root_element.htm)

---

# B. Modules and scanning (object repository equivalent)

### Module (XModule)

**What it does**  
Stores technical information Tosca uses to steer the AUT; shared across TestCases.

**How Tosca provides it**  
**Modules** tab; **ModuleAttributes** (parameters for Standard subset, or scanned controls). Creating a **TestStep** from a Module spawns **TestStepValues** per attribute.

**Typical workflow**
```text
Create/select Module → drag onto TestCase (or Fuzzy Search Ctrl+T)
→ TestStep appears → fill TestStepValues (ActionMode + Value)
→ run
```

**Evidence:** [Create and manage Modules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm)

---

### Module sources

| Source | How obtained | Evidence |
|--------|----------------|----------|
| **Standard subset** | Prebuilt Modules (e.g. `OpenUrl`, TBox tools) | [Modules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm) |
| **XScan – Application** | GUI scan (Ctrl+Shift+A) | [Scan modules overview](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/scan_modules_overview.htm) |
| **XScan – API** | API Scan (Ctrl+Shift+I) | Same |
| **XScan – Mobile** | Mobile Engine 3.0 (Ctrl+Shift+M) | Same |
| **ARA** | Record interactions → Modules/TestCase | [ARA menu](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/recorder/recorder_menu.htm) |
| **Manual Module** | Documented as creation path in Modules topic | [Modules](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm) |

**Maintenance (documented):** Rescan, Merge, Replace Modules; embed SpecialExecutionTasks.

---

### Tosca XScan (web/desktop GUI)

**What it does**  
Captures controls from running applications into a new Module.

**How Tosca provides it**  
Select application → auto or manual **engine** selection (right-click) → **Scan Screen** / select controls → **Finish Screen** saves Module. Web: **Tricentis Automation Extension**; **Freeze Page** for transient DOM. Zoom 100%/125% on Windows display for web scans (documented).

**Typical workflow**
```text
Modules → Scan → Application → pick window → scan controls
→ Finish Screen → Module in repository → use in TestCases
```

**Evidence:** [Start scanning](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/xscan_start_scan.htm), [Select controls](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/xscan_select_controls.htm)

**Additional scan entry points (documented):** PDF Engine 3.0, Remote Terminal (Host/PuTTY), **WebDriver** (More menu), **Salesforce Scan**, **File Scan** (JSON/XML), **Vision AI** engine in XScan.

---

# C. TestCases, TestSteps, and steering

### TestCase composition

**What it does**  
Defines a test as an ordered sequence of **TestSteps** (Module instances).

**How Tosca provides it**  
**TestCases** tab; Details view lists **TestStepValues** (per control/attribute) with **ActionMode** and **Value** columns.

**Evidence:** [Creating demo TestCase](https://docs.tricentis.com/tosca-2026.1/en-us/content/tutorial/creating_test_cases.htm)

---

### ActionModes (steering semantics)

**What it does**  
Defines how a **Value** is applied to a control (read vs write vs wait vs buffer).

**How Tosca provides it**  
Per **TestStepValue**; available modes depend on **InterfaceType** of XModule.

| ActionMode | Documented behavior (summary) |
|------------|-------------------------------|
| **Input** | Transfer value / click / select (incl. ValueRange dropdowns) |
| **Verify** | Check value or property (`.enabled==True`, regex, case-sensitive) |
| **WaitOn** | Pause until property/value matches; timeout from **Synchronization Timeout during WaitOn** setting |
| **Buffer** | Read/store values into global buffer |
| **Constraint** | Limit search for parent/superordinate node (tables, XML) |
| **Select** | Select uniquely named nodes (documented in ActionModes topic) |

**Typical workflow**
```text
TestStepValue → set ActionMode Verify + Value "360.00" on price field
→ execution compares control default property or specified property
```

**Evidence:** [ActionModes](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/action_modes.htm), [Steering Controls](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/steering_controls.htm), [Control properties](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/control_properties.htm)

---

### Buffers and dynamic data

**What it does**  
Store runtime values for later Input/Verify across steps.

**How Tosca provides it**  
- **ActionMode Buffer** on TestStepValues  
- **TBox Set Buffer** / Buffer Operations standard modules  
- Read syntax: `{B[Buffername]}` in text expressions  
- **Test Data Service (TDS)** modules: find/read rows → `{TDS[alias.attribute]}` into buffers  

**Evidence:** [Text expressions](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/text_expressions.htm), [Buffer Operations](https://docs.tricentis.com/tosca-2026.1/en-us/content/standard_subset/automation_tools/buffer_operations.htm), [TestData Expert module](https://docs.tricentis.com/tosca-2026.1/en-us/content/test_data_management/tds_expert_module.htm)

---

### Test configuration parameters (TCPs)

**What it does**  
Parameterize tests and override engine/settings per object.

**How Tosca provides it**  
**Test configuration** tab on TestCases, folders, ExecutionLists, **Configurations** bundles. Custom TCP referenced as `{CP[Name]}`. Out-of-the-box TCPs (browser, paths, SelfHealing, etc.) act without explicit reference. Settings override: copy setting path into TCP **Name**.

**Evidence:** [Create TCPs](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/tcp_create_tcp.htm), [Override settings](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/settings_hierarchy.htm), [Configurations](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/tcp_create_configurations.htm)

---

# D. Model-based design (TestCase-Design)

### TestCase-Design AddIn

**What it does**  
Plan coverage before building executable TestCases: combinations, classes, instances, templates.

**How Tosca provides it**  
**TestSheets** (attributes/steps/instances), **Classes**, generation of **TestCases** and **TestCase Templates**, assignment of TestSheets to templates for instantiation. Requires license; can run read-only if AddIn disabled.

**Typical workflow**
```text
Requirement → TestSheet (attributes + instances or process steps)
→ generate instances / TestCase templates
→ bind to executable TestCases for execution
```

**Evidence:** [TestCase-Design intro](https://docs.tricentis.com/tosca-2026.1/en-us/content/testcase_design/testcase_design_intro.htm), [TestSheets](https://docs.tricentis.com/tosca-2025.1/en-us/content/testcase_design/testsheets.htm)

---

### Tosca Cloud design models

**What it does**  
Cloud-native design: **process-based** (business flows) and **template-based** (blueprint test case + test sheet → multiple instances).

**Evidence:** [Cloud test case design](https://docs.tricentis.com/tosca-cloud/en-us/content/testcase_design/overview.htm)

---

# E. Test data management

### Test Data Service (TDS)

**What it does**  
Central test data repository; Modules like **TestData - Expert** search/create items; constraints with **ActionMode Constraint**; buffer results.

**How Tosca provides it**  
TDS expressions in values; date comparisons via ISO 8601 strings; numeric DataTypes for constraints.

**Evidence:** [TDS Expert module](https://docs.tricentis.com/tosca-2026.1/en-us/content/test_data_management/tds_expert_module.htm), [TDS example](https://docs.tricentis.com/tosca-2026.1/en-us/content/test_data_management/tds_example.htm)

---

### Cloud test data modules

**What it does**  
Standard modules for dataset CRUD, find/read rows, lock/unlock, buffer via `{TDM[alias][column]}`.

**Evidence:** [Test data modules (Cloud)](https://docs.tricentis.com/tosca-cloud/en-us/content/references/modules/automation_modules/testdata_modules.htm)

---

# F. API automation

### Tosca API Scan

**What it does**  
Scan API definitions, build messages/operations, create TestCases/Modules, integrate with **API Connection Manager** and **OSV** (service virtualization).

**How Tosca provides it**  
Standalone **ApiScan.exe** or Commander **API Testing > API Scan**; scan **file** (JSON, RAML, WSDL, XML, XSD, …), **URI**, or **OData**; export messages to Commander/OSV.

**Supported standards (documented table):** REST, SOAP, HTTP, GraphQL-related transports, JMS, Kafka, MQ families, OpenAPI 3.0, Swagger, WADL, WSDL, OAuth2, Kerberos, etc.

**Typical workflow**
```text
API Scan → import WSDL/OpenAPI/URI → generate operations
→ export to Commander as API Modules/messages
→ compose TestCase TestSteps → ExecutionList
```

**Evidence:** [API Scan overview](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/api_scan.htm), [Scan file](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/api_scan_file.htm), [Launch API Scan](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/api_scan_launch.htm)

---

# G. Web, desktop, mobile automation

### XBrowser / web (Engines 3.0)

**What it does**  
Steer web applications via scanned XBrowser modules; extension-assisted identification.

**How Tosca provides it**  
XScan with browser + **Tricentis Automation Extension**; optional **WebDriver** scan path for remote/local browsers.

**Evidence:** [XScan start](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/xscan_start_scan.htm), [Scan overview – WebDriver](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/scan_modules_overview.htm)

---

### Desktop (UIA, WinForms, WPF, Java, …)

**What it does**  
Scan and steer native Windows/Java desktop controls via engine-specific modules.

**How Tosca provides it**  
XScan engine selection (e.g. **UIA**); steering patterns in engine chapters (tables, context menus via **TBox Context Menu** when menu is open).

**Evidence:** [UIA controls](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/uia/tbox_uia_engine_controls.htm), [WinForms controls](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/dotnet/tbox_dotnet_controls.htm)

---

### Mobile Engine 3.0

**What it does**  
Automate native, hybrid, and mobile web on Android/iOS.

**How Tosca provides it**  
**Mobile Scan**: connections **Local/Remote TMA**, **Cloud (Appium)**, **Virtual Mobile Grid**; hybrid **Native vs Web** context; optional viewport-only scan; **IBTA Scan** and **Vision AI** scan modes.

**Engines:** Mobile Engine 3.0 (native/hybrid), **Mobile Web Engine 3.0** (default for mobile web), legacy mobile web via TCP `UseLegacyMobileWeb`.

**Evidence:** [Mobile scan start](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/mobile/mobile_scan_start.htm), [Mobile Engine 3.0](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/mobile/tbox_mobileweb.htm), [Mobile image-based](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/mobile/tbox_mobile_image_based_automation.htm), [Mobile Vision AI](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/mobile/tbox_mobile_vision_ai_automation.htm)

---

### SAP, Salesforce, PDF, Host, Excel

**What it does**  
Technology-specific scan/steer paths (documented under scan overview and engine landing pages).

**Evidence:** [Scan modules overview](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/scan_modules_overview.htm), [Excel engines](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/excel/excel_engines_landing.htm)

---

# H. Resilience: self-healing, recovery, synchronization

### Self-healing Mode (Engines 3.0)

**What it does**  
On control lookup failure, search AUT for a **similar** control using stored **self-healing properties**; continue execution if match found.

**How Tosca provides it**  
Properties auto-added on **ModuleAttribute** when scanning via XScan, Rescan, Salesforce Scan, or ARA (ARA may require rescan for weights). Enable via TCP **`SelfHealing`**: `Weighted` (default threshold TCP **`SelfHealingWeightThreshold`** = 0.75), `Combination`, or `False`. Supported engines listed (DotNet, Java, Oracle, UIA, XBrowser, Salesforce). Post-run: **Apply Self-Healing properties** from ExecutionLog to update Module.

**Typical workflow**
```text
Scan Module (self-healing props captured)
→ enable SelfHealing TCP on TestCase
→ execution misses control → weighted/combination match → steer alternate
→ review log → optionally apply props to Module
```

**Evidence:** [Self-healing TestCases](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/selfhealing.htm), [Cloud self-healing](https://docs.tricentis.com/tosca-cloud/en-us/content/references/selfhealing.htm)

---

### Engines 3.0 Recovery & CleanUp

**What it does**  
**Recovery Scenarios:** corrective TestSteps on failure; **CleanUp Scenarios:** reset environment if recovery fails.

**How Tosca provides it**  
Scenarios attached in TestCases hierarchy; **RetryLevel** (TestStepValue / TestStep / TestCase) controls when recovery runs and where execution resumes. Docs recommend **RetryLevel TestStep** in many cases.

**Evidence:** [Create Recovery Scenario](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/recovery_create_scenario.htm), [CleanUp Scenario](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/recovery_cleanup.htm), [Error handling best practices](https://docs.tricentis.com/tosca-2026.1/en-us/content/best_practices/testcases_error_handling.htm)

---

### Synchronization

**What it does**  
Wait for UI state before proceeding.

**How Tosca provides it**  
Primarily **ActionMode WaitOn** with property/value expressions; timeout tied to **Synchronization Timeout during WaitOn** (settings). Separate engine/TCP tuning documented under settings hierarchy.

**Evidence:** [ActionModes – WaitOn](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/action_modes.htm)

---

# I. Vision AI, image-based automation, OCR

### Tricentis Vision AI (scan & steer)

**What it does**  
Scan/steer by **visual** controls using server-side neural networks; includes **OCR** for labels.

**How Tosca provides it**  
XScan → right-click app → **Vision AI** engine; steering params **`ScanControlNetwork`**, **`ScanOcrNetwork`**; fuzzy label matching; **UIDCs** (user-identified controls) with anchor points. Cloud: similar flow with review of self-healing changes post-run.

**Evidence:** [Vision AI scan (Commander)](https://docs.tricentis.com/tosca-2026.1/en-us/content/vision_ai/vision_ai_scan_modules.htm), [Vision AI (Cloud)](https://docs.tricentis.com/tosca-cloud/en-us/content/create_tests/scan_vision_ai.htm)

---

### Image-Based Test Automation (IBTA)

**What it does**  
Identify controls by **screenshot templates** (not DOM).

**How Tosca provides it**  
XScan Advanced → **ADVANCED IDENTIFICATION > IMAGE**; capture identifier image; **Accuracy** % and **Method** (Fullscreen / Controlbased / Auto). Mobile: **IBTA Scan** in Mobile Scan.

**Evidence:** [Identify by image](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/xscan_identify_by_ibta.htm), [Mobile IBTA](https://docs.tricentis.com/tosca-2026.1/en-us/content/engines_3.0/mobile/tbox_mobile_image_based_automation.htm)

---

### OCR (Tesseract) for image text verify

**What it does**  
Verify text rendered in image-based controls using **Tesseract** OCR (default verify path for image text; UIA alternative documented).

**How Tosca provides it**  
**Verify** with **OCRText** property; global **Settings → TBox → OCR** (EngineName Tesseract, monochrome, segmentation, SmartOCR retry sequence). Steering params/TCP can override.

**Evidence:** [Image-Based Test Automation](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/optical_intelligence_generic.htm), [Settings OCR](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/settings_ocr.htm)

---

# J. Recording (Tricentis ARA)

### Automation Recording Assistant

**What it does**  
Record user actions into TestSteps while using the AUT (modes: record, verify, image-based step, manual step).

**How Tosca provides it**  
ARA overlay UI; pause/resume/stop; playback before save; saves TestCases/Modules into workspace (Cloud: unsupported manual steps). Included with Tosca 15.0+ install.

**Typical workflow**
```text
Launch ARA → record interactions → optional verify/image steps
→ playback → save to Commander repository
```

**Evidence:** [ARA menu](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/recorder/recorder_menu.htm), [Create TestCases with ARA](https://docs.tricentis.com/ara-1.16/en-us/content/recorder/recorder_xscan.htm), [Cloud ARA](https://docs.tricentis.com/tosca-cloud/en-us/content/create_tests/scan_ara.htm)

---

# K. Execution model

### ScratchBook

**What it does**  
Ad-hoc trial execution of TestSteps/TestCases/folders.

**How Tosca provides it**  
**Ctrl+B**; drag objects; **F6** run; **does not persist** structure or results (unlike ExecutionLists).

**Evidence:** [ScratchBook](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_scratchbook.htm)

---

### ExecutionLists

**What it does**  
Planned, repeatable runs with **persistent** results.

**How Tosca provides it**  
Drag TestCase/folder onto **ExecutionLists** → **ExecutionEntries**; each run adds **TestCaseLog**; **ActualLog** holds latest overall result; **ArchivedLog** for snapshots; export/trends documented.

**Typical workflow**
```text
Build TestCases → create ExecutionList → configure TCPs on list
→ Run → analyze ActualLog / TestCaseLogs → archive/export
```

**Evidence:** [Execution overview](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_overview.htm), [ExecutionLists](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_lists_section.htm), [Execution results](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_results.htm)

---

### Tosca Distributed Execution (DEX)

**What it does**  
Distribute ExecutionLists across **Agents** via **Distribution Server** and **AOS** workspace; supports unattended RDP execution.

**How Tosca provides it**  
Multi-user workspace required; **TestEvents** bundle ExecutionLists + **Configurations** (hardware/software matching); **DEX Monitor** for status/cancel. 2026.1 LTS: DEX **not enabled by default** — manual activation documented.

**Typical workflow**
```text
Check in assets → create TestEvent → assign Configurations + ExecutionLists
→ Execute now → DEX Server schedules Agent → results to common repository
```

**Evidence:** [DEX overview](https://docs.tricentis.com/tosca-2026.1/en-us/content/distributed_execution/distributed_execution_overview.htm), [TestEvents](https://docs.tricentis.com/tosca-2026.1/en-us/content/distributed_execution/create_and_execute_testevents.htm), [Execution section](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_section.htm)

---

### Manual, exploratory, interactive testing

**What it does**  
Non-automated or hybrid execution paths in **Execution** tab (folders documented).

**Evidence:** [Execution section objects](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_section.htm) (Exploratory Testing, Interactive Testing folders)

---

# L. Reporting and analytics (Commander vs platform)

| Capability | **Tosca Commander (local/multi-user repo)** | **Cloud / platform** |
|------------|---------------------------------------------|-------------------------|
| Execution logs | ActualLog, TestCaseLog, ArchivedLog, screenshots via settings/TCP | Playlist run APIs, JUnit export (Jenkins doc) |
| Trends / Excel export | Documented in execution results | Cloud dashboards (outside this pass—see Cloud admin topics) |
| Self-healing audit | ExecutionLog detail + apply to Module | Post-run review in Cloud self-healing topic |

**Evidence:** [Execution results](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/execution_results.htm)

---

# M. CI/CD and headless execution

### Tosca CI/CD integration types (documented)

1. **Elastic Execution Grid + Tosca Cloud** (recommended): cloud-native CI hookup.  
2. **Tosca Execution Client** script (`tosca_execution_client.ps1` / `.sh`) against DEX + AOS with OAuth client ID/secret.  
3. **Tosca Server Execution API** (DEX/AOS or Elastic Grid).  
4. Legacy: **Tosca CI Client**, Remote Execution Service, Jenkins plugin.

**Evidence:** [CI/CD concept](https://docs.tricentis.com/tosca-2026.1/en-us/content/continuous_integration/concept.htm), [Execution clients](https://docs.tricentis.com/tosca-2025.1/en-us/content/continuous_integration/tosca_execution_clients.htm), [CI client config](https://docs.tricentis.com/tosca-2026.1/en-us/content/continuous_integration/set_up_ci_dex_client_config.htm)

---

### Jenkins + Tosca Cloud playlists

**What it does**  
Trigger playlist runs via REST (`/_playlists/api/v2/playlistRuns/`) with OAuth token; poll status; fetch JUnit.

**Evidence:** [Run playlists in Jenkins](https://docs.tricentis.com/tosca-cloud/en-us/content/admin_guide/run_playlists_jenkins.htm)

---

# N. Integrations

### qTest

**What it does**  
ALM/release planning in qTest with automated execution/results from Tosca.

**How Tosca provides it**  
Dedicated **closed** qTest workspace; Notification Service; DEX; **Link with qTest** on TestCases; **Create TestEvent for qTest** on ExecutionLists; status mapping in qTest automation settings.

**Evidence:** [qTest integration intro](https://docs.tricentis.com/tosca-2026.1/en-us/content/qtest_integration/qtest_integration_intro.htm), [Link TestCases](https://docs.tricentis.com/tosca-2026.1/en-us/content/qtest_integration/link_testcases.htm), [qTest TestEvents](https://docs.tricentis.com/tosca-2026.1/en-us/content/qtest_integration/create_qtest_testevents.htm)

---

### OSV (service virtualization)

**What it does**  
Virtualize services; API Scan exports scenarios/messages to OSV.

**Evidence:** [API Scan – OSV](https://docs.tricentis.com/tosca-2026.1/en-us/content/tbox/api_scan.htm)

---

# O. Standard subset & extensibility

### Standard subset / TBox modules

**What it does**  
Prebuilt Modules for common actions (buffers, Windows ops, API helpers, etc.) without scanning.

**How Tosca provides it**  
Imported via workspace template **Standard subset**; compose like any Module.

**Evidence:** [Buffer Operations](https://docs.tricentis.com/tosca-2026.1/en-us/content/standard_subset/automation_tools/buffer_operations.htm), [Workspace template](https://docs.tricentis.com/tosca-2026.1/en-us/content/installation_tosca/workspaces_create_singleuser.htm)

---

### SpecialExecutionTasks / expert modules

**What it does**  
Embed specialized execution tasks into Modules (referenced in Modules topic).

**Evidence:** [Modules – Embed SpecialExecutionTasks](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/modules_section_orange.htm)

---

# P. Tosca Cloud (documented slice)

| Feature | Mechanism |
|---------|-----------|
| Asset inventory | **Build > Modules**; scan web/desktop/Vision AI/ARA |
| Test design | Business flows + template/test sheet generation |
| Execution | Playlists, agents, private team agents |
| Self-healing | Parameter `Selfhealing` = Weighted/Combination on test case |

**Evidence:** [Cloud XScan](https://docs.tricentis.com/tosca-cloud/en-us/content/create_tests/scan_xscan.htm), [Cloud design overview](https://docs.tricentis.com/tosca-cloud/en-us/content/testcase_design/overview.htm)

---

# Q. AI capabilities (official, non-marketing)

| Capability | Input | Processing (per docs) | Output |
|------------|-------|------------------------|--------|
| **Vision AI** | Live app UI | Server neural nets for control detection + OCR networks | Vision-based Modules; self-healing props |
| **Self-healing** | ModuleAttribute technical props | Weighted or combination matching against AUT | Alternate control steered; optional Module update |
| **Mobile Vision AI** | Mobile live view | Cloud URL or personal access token (settings/TCP for DEX) | Mobile Vision AI modules |

Tosca docs do **not** describe an LLM code-generation assistant analogous to Katalon AI Assistant in the topics reviewed.

---

# R. Commander vs Cloud vs on-prem execution (comparison axis)

| Dimension | Tosca Commander + DEX | Tosca Cloud |
|-----------|----------------------|-------------|
| Primary artifacts | Modules, TestCases, ExecutionLists, TestEvents | Modules, test cases, playlists |
| Scan | Full XScan engine menu | Web/desktop/Vision/ARA paths |
| CI | Execution Client / Server API / legacy CI | Playlist REST + Jenkins examples |
| Workspace | `.tws`, multi-user check-in | SaaS project |

---

## Appendix: XScan scan menu (documented options)

- Application (GUI)  
- API (API Scan)  
- Mobile  
- PDF  
- Remote Terminal (Host / PuTTY)  
- More: WebDriver, Salesforce, File Scan  

**Evidence:** [Create Modules by scanning](https://docs.tricentis.com/tosca-2026.1/en-us/content/tosca_commander/scan_modules_overview.htm)

---

## Katalon comparison hooks (for later merge)

| Topic | Tosca (this doc) | Katalon (sibling doc) |
|-------|------------------|------------------------|
| Reuse unit | Module → TestStep | Object Repository → `findTestObject` |
| Step data | TestStepValue + ActionMode | Keyword + object/input columns |
| Design-time combinatorics | TestCase-Design TestSheets | Data binding / internal-external test data |
| Scan | XScan engines | Recorder/Spy |
| Self-heal | Property weighting on controls | Alternate locators + LLM healing |
| CI | DEX TestEvents / Cloud playlists | KRE CLI |

---

*Generated for comparative R&D. Verify version-specific behavior (2026.1 LTS vs Cloud) against current Tricentis release notes before procurement decisions.*
