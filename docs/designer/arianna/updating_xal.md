# Updating XAL

When the scenario changes after the XAL has already been generated, use **Update XAL** to propagate the changes to the existing file without regenerating from scratch.

---

## When Update XAL appears

The action button in the scenario tab changes label and colour based on the alignment state of the two artefacts:

| Button label | Colour | Meaning |
|---|---|---|
| **Generate XAL** | Green | No XAL file exists yet, or the existing XAL was uploaded manually without a generation history |
| **Update XAL** | Green | Scenario and XAL are aligned — the button is available to regenerate if needed |
| **Update XAL** | Orange | The scenario has been modified since the last generation — the XAL is out of date |

The orange colour is a visual signal that the two artefacts are out of sync. Click **Update XAL** to bring the XAL back in line with the scenario.

---

## What is different from Generate XAL

When generating for the first time, Arianna builds the automaton graph from scratch. When updating, it receives the **existing graph** alongside the new scenario and produces only the changes required — preserving unchanged states, transitions, and node identifiers.

This surgical approach avoids unnecessary restructuring of an automaton that may already be partially or fully correct.

The context Arianna receives depends on what is available in the current session:

| Available | Context passed to Arianna |
|---|---|
| `.dot` file from a previous generation | New scenario + existing `.dot` (preferred — most structural context) |
| No `.dot`, but XAL exists | New scenario + existing XAL (used as structural reference) |

The `.dot` file is retained internally by the session after each generation. It is not shown in the repository explorer, but it is used automatically by **Update XAL** when available.

---

## After updating

The XAL file is overwritten with the new version and reloaded in the graph. The alignment indicator returns to green.

If the update introduces validation errors, the same [automatic retry and download fallback](generating_xal.md#if-all-attempts-fail) as a fresh generation applies.
