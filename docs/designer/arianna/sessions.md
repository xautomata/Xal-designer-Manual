# Chat Sessions

The AI Assistant panel can hold multiple chat sessions at the same time. Each session has its own history, scenario, and XAL context — switching between them is instantaneous.

---

## The sessions panel

Click the **sessions icon** in the chat header to open the sessions panel. It lists all active sessions, divided into two groups:

- **File sessions** — linked to a specific XAL file in the repository
- **System sessions** — always-present sessions for general assistance (see below)

Click any session in the list to switch to it. If the incoming session has a scenario, its scenario tab opens in the editor; the previous scenario tab is closed first (with a save prompt if it was in edit mode with unsaved changes).

---

## Managing file sessions

### Creating a session
Right-click any `.xal` or `.scenario.md` file in the **Repository Explorer** and select **New chat**. One session per file — if a session already exists for that file, **Open chat** appears instead and the existing session is reactivated.

### Resetting a session
Select **Reset history** from the session menu to clear the chat messages while keeping the linked file, the scenario, and the scenario tab intact.

Use this to free up context tokens when the conversation has become long but the scenario is still good.

### Deleting a session
Select **Delete** from the session menu. The session and its history are removed; the `.scenario.md` and `.xal` files in the repository are not affected.

---

## System sessions

!!! note "Coming soon"
    System sessions are not yet available. This section describes the planned behaviour.

Two sessions will always be present in the panel and cannot be deleted or renamed. They will be created automatically on first launch and restored if the browser storage is cleared.

| Session | What it does |
|---|---|
| **Interface Guide** | Answers questions about the XAL Designer interface — panels, buttons, workflows, and navigation |
| **XAL Guide** | Answers questions about the XAL language — syntax, state and transition types, actions, metrics, and parameters |
| **Java Action Guide** *(planned)* | Guides you through implementing the Java methods behind XAL actions — generates stub code and suggests implementations based on a curated cookbook of patterns |

These sessions have no linked file and no scenario tab. They operate independently of whatever file you are currently working on — use them to look something up without interrupting your current session.

---

## Skills

Each file session has a **skill selector** in the chat header. A skill is a predefined mode that controls the instructions Arianna receives and the type of output it produces. The correct skill is selected automatically based on the current state of the artefacts, but you can change it manually at any time.

| Skill | Auto-selected when | What Arianna does |
|---|---|---|
| **Build scenario** | No scenario exists yet | Guides you through describing the automaton and generates a structured scenario |
| **Update scenario** | Scenario exists, XAL is absent or out of date | Helps you refine the scenario before (re)generating the XAL |
| **Consulenza** | Scenario and XAL are aligned | Answers operational questions about the automaton — what a state means, what triggers a transition, how to adjust a threshold — without changing artefacts unless you explicitly ask |

!!! info
    Switching skill does not lose the conversation history. The next message you send will simply use the new skill's instructions.

---

## Guided flows

Several actions in Arianna trigger **guided flows** — sequences of steps that Arianna carries out automatically or prompts you to carry out, without requiring you to navigate the interface manually.

### Post-generation flow (Generate scenario from XAL)

After generating a scenario from an existing XAL, Arianna sends a prompt in the chat inviting you to verify consistency, with an inline **Verify Consistency** button. After the check, a second message sets the working direction for the session (see [Generate Scenario from XAL](scenario_from_xal.md)).

### Post-sync prompt

After **Sync Scenario** completes, Arianna sends a short message in the chat suggesting a consistency check — a reminder that syncing updates the scenario to match the XAL, but does not guarantee that no details were lost.

### Automatic retry on XAL validation failure

When **Generate XAL** or **Update XAL** produces an invalid XAL, Arianna retries automatically up to two additional times (three attempts total), passing the full accumulated error history to Claude on each attempt. The retry is silent — you see only the status message updating to show the current attempt number.

If all three attempts fail, the fallback flow provides:

- the validation errors in plain text, so you can understand what went wrong
- a **Download XAL** button for the last generated file — often nearly correct
- a **Download .dot** button (under *Advanced*) for users who want to correct the GraphViz source manually

### Inline action buttons

In some chat messages — particularly those generated by guided flows — Arianna includes **inline action buttons** directly in the message. These trigger the corresponding Designer action (such as *Verify Consistency*) without requiring you to find the button in the scenario tab. Clicking the inline button has exactly the same effect as clicking the button in the UI.
