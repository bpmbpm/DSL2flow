# DSL2flow User Manual

## Overview

**DSL2flow** is a browser-based application for modelling and simulating BPMN processes using the DSL2 (Domain-Specific Language 2) text format. It lets you describe a business process as text, automatically generates an EPC2 diagram, a state table, and executable JavaScript, then lets you interactively simulate the process step by step.

---

## Interface

### Header

- **EN / RU button** — switch the UI language between English and Russian.

### Left Column

| Panel | Description |
|---|---|
| **Load Example** | Pick and load a ready-made DSL2 example |
| **Active Role** | Choose the role that performs actions |
| **Process Status** | Current function, role, workflow token, tick, document states |
| **Actions** | Buttons for executing the current process step |
| **Controls** | Start/restart the process, export the table to Excel |

### Right Column (tabs)

| Tab | Description |
|---|---|
| **EPC2 Diagram** | Auto-generated process diagram (EPC2 notation, Mermaid) |
| **State Table** | Execution trace: each step with tokens and document transitions |
| **DSL Source** | Editable DSL2 source code; button to re-translate |
| **Generated JS** | JavaScript engine code auto-generated from DSL2 |

### Bottom Panel — Status / Log

- Log of all activity: initialisation, start, event firings, errors.
- **Save Log** — downloads the log as a `.txt` file via the browser's native save dialog.
- **Clear** — clears the log content.
- The panel is **resizable** — drag the bottom-right corner to resize.

---

## Loading an Example

1. In the **"Load Example"** panel, choose a file from the dropdown:
   - `example_en.dsl2` — English example (Leave Request Approval)
   - `example_ru.dsl2` — same process in Russian
2. Click the **"Load"** button.
3. The DSL code will appear in the **DSL Source** tab and the diagram will be built automatically.
4. Click **"▶ Start / Restart Process"** to begin the simulation.

---

## Main Actions

### 1. Starting the Process

1. Load an example or enter your own DSL2 in the **DSL Source** tab.
2. In the **"Active Role"** panel, pick a role (or "Supervisor" to perform any function).
3. Click **"▶ Start / Restart Process"**.

### 2. Executing Steps

- The **"Actions"** panel shows available actions for the current step.
- Click an action button (e.g. "Согласовано / Approve" or "Отказано / Reject").
- System functions (`role: System`) execute automatically after a 0.6-second delay.
- Switch the role in the dropdown if the current step requires a different role.

### 3. Viewing the Trace

- Switch to the **"State Table"** tab for the full execution trace.
- Click **"⬇ Excel"** to export the table as an `.xlsx` file.

### 4. Editing DSL

1. Switch to the **DSL Source** tab and edit the code.
2. Click **"⟳ Re-translate & Restart"** — the code is recompiled and the process restarts.

---

## DSL2 Syntax

```
process <ProcessName>:
  title: "<Title>"

  documents:
    <DocumentName>:
      states:
        - state1
        - state2
        - ...
      transitions:
        state1 --> state2 --> state3
        state2 --> rejected

  roles:
    - Role1
    - Role2
    - ...

  workflow:
    start: <StartEvent>

    steps:
      - function <FunctionName>:
          role: <Role>
          system: <SystemName>
          input_doc:
            - <Document>.<state>
          output_doc:
            - <Document>.<state>
          on_complete:
            - event: <EventName>
              condition: "decision == 'approved'"  # optional
              output_doc: <Document>.<state>        # optional
              next: <NextFunction | end>

    end: <EndEvent>
```

### Document Transitions

The `documents` section should explicitly list all possible document state transitions using mermaid notation:

```
transitions:
  template --> submitted --> manager_approved --> director_approved
  manager_approved --> rejected
  director_approved --> rejected
```

This makes it clear that the `rejected` state is reachable from two different states.

---

## Example Process: Leave Request Approval

**Participants:**
- `Applicant` — fills in and submits the leave request
- `Manager` — approves or rejects
- `Director` — final approval
- `System` — sends notifications

**Routes:**
1. Applicant submits → Manager approves → Director approves → Approval notification
2. Applicant submits → Manager rejects → Rejection notification
3. Applicant submits → Manager approves → Director rejects → Rejection notification

---

## Additional Resources

- `ver1/example/example_en.dsl2` — English example
- `ver1/example/example_ru.dsl2` — Russian example
- `ver1/example/example_epc2_en.md` — EPC2 diagram (English)
- `ver1/example/example_epc2_ru.md` — EPC2 diagram (Russian)
- `ver1/BPMN_DSL_v1.md` — DSL usage examples for BPMN
- `ver1/xstate_petri_analysis.md` — XState and Petri-net analysis
- `ver1/test/test_runner.html` — automated test with log file download
