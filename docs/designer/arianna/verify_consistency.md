# Verifying Consistency

**Verify Consistency** runs a systematic comparison of the scenario and the XAL file, checks that every element described in the scenario has a corresponding construct in the XAL, and reports any discrepancies.

---

## When to use it

Run a consistency check:

- after generating a scenario from an existing XAL, to catch any misinterpretations in the reverse generation
- after syncing the scenario, to confirm that the update captured all the changes
- before committing, as a final validation that the two artefacts agree

---

## Triggering the check

Click **Verify Consistency** in the action bar of the scenario tab. Arianna performs the check as a one-shot operation outside the normal chat history and delivers the report as a message in the chat.

The report covers:

- states and transitions present in one artefact but missing in the other
- action or metric names that do not match between scenario and XAL
- clock constraints described in the scenario that are not reflected in the transitions
- global state variables listed in the scenario that are absent or typed differently in the XAL

!!! info "Bias towards coherence"
    Arianna's consistency check is calibrated to avoid false positives. When a discrepancy is ambiguous — for example, a description that could match more than one XAL element — it is not flagged as an error. Only clear mismatches are reported.

---

## Acting on the report

Read through the report in the chat and decide how to resolve each issue. Depending on which artefact should be considered the source of truth:

- **Scenario is correct → XAL needs to change:** edit the automaton in the Designer and re-run the generation pipeline, or fix the XAL manually.
- **XAL is correct → scenario needs to change:** ask Arianna in the chat to update the affected sections, or use [Sync Scenario](syncing_scenario.md).

You can ask Arianna to help resolve any item in the report — the chat context already contains both artefacts after the check.
