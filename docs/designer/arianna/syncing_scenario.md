# Syncing the Scenario

When you edit an automaton directly in the Designer — adding states, changing transitions, or adjusting parameters — the XAL changes but the scenario does not. Use **Sync Scenario** to update the scenario to reflect the current state of the XAL.

---

## When to use it

Sync Scenario is useful when:

- you have refined the automaton graph manually after a generation and want the scenario to remain an accurate description
- you have received an XAL file from another team member and want to produce a scenario for it
- you want to keep the two artefacts aligned before committing

---

## Triggering the sync

Click **Sync Scenario** in the action bar of the scenario tab. Arianna reads the current XAL content and updates only the parts of the scenario that correspond to what changed — it does not rewrite the entire document.

The precision of the update depends on what is available:

| Situation | What Arianna receives | Result |
|---|---|---|
| XAL has been modified since the last generation | Existing scenario + diff of the XAL changes | Targeted update — only the affected sections of the scenario change |
| First sync of the session (no diff baseline) | Existing scenario + full current XAL | Full-context update — Arianna uses the existing scenario as a style and structure reference |
| No scenario exists yet | Full current XAL only | Full reconstruction — the scenario is written from scratch |

---

## After syncing

The scenario tab is updated with the new content and the `.scenario.md` file is overwritten. Arianna then sends a short message in the chat suggesting a consistency verification — a recommended step after any sync to confirm that no details were lost in translation.

!!! info "The XAL is the source of truth during a sync"
    Sync Scenario always moves the scenario towards the XAL, never the other way. If you want to update the XAL to match a changed scenario, use [Update XAL](updating_xal.md) instead.
