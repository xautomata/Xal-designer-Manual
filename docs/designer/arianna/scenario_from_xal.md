# Generate Scenario from XAL

If you have an existing XAL file with no associated scenario, Arianna can reconstruct one by reading the automaton structure.

---

## When to use it

This flow is useful when:

- you are working with a legacy XAL file that was created manually or by another tool
- you received an XAL file and want a human-readable description of what it does
- you want to start a [consultation session](sessions.md#chat-modes) on an existing automaton

---

## Starting the flow

Open a chat session for the XAL file (right-click → **Open chat** or **New chat**). If the file has no scenario yet, the chat area shows a single call-to-action:

**Generate scenario from XAL**

Click it. Arianna reads the full XAL content and generates a structured scenario. The `.scenario.md` satellite file is created and the scenario tab opens.

---

## The guided verification flow

Reconstructing a scenario from XAL is a reverse operation — Arianna infers intent from structure, which can introduce imprecisions, especially in complex automata with non-obvious logic.

To help you catch these issues immediately, Arianna automatically initiates a guided flow after the scenario is generated:

**Step 1 — Post-generation prompt**

Arianna sends a message suggesting that the first generated scenario may contain inaccuracies, with a **Verify Consistency** button inline in the message. You do not need to find the button in the scenario tab — it is right in the chat.

**Step 2 — Consistency check**

Click the inline button to run the check. The consistency report appears in the chat as usual.

**Step 3 — Direction message**

After the report, Arianna sends a second message establishing the working direction for the session:

> *"The XAL is the source of truth. Any inconsistencies should be resolved by updating the scenario — not the XAL. Ask for help refining it."*

This message stays in the chat history and informs all subsequent responses: when you ask Arianna to fix a discrepancy, it will propose changes to the scenario, leaving the XAL intact.

---

## Refining the scenario

Once the consistency check is done, refine the scenario by describing corrections in the chat. The XAL remains unchanged throughout — the goal of this flow is to produce a scenario that accurately describes what the XAL does, not to change the XAL.

When the scenario is accurate, you can switch to [Consulenza mode](sessions.md#chat-modes) to ask operational questions about the automaton, or commit both files to the repository.
