# Configuring State Actions

An action associated with a state defines an operation the automaton performs when it enters that state. Actions interact with external systems — APIs, ERP platforms, warehouse management systems, or any other integration target.

To configure an action, select a state on the canvas and expand the **Action** section in the right panel.

---

## Query

The **Query** field is an optional SQL-like expression used by some action types to retrieve context before executing. Not all actions require a query — leave this field empty if the action operates solely through its system file and input parameters.

---

## Action Data configuration

The **Action Data** section specifies how the action is executed.

| Field | Description |
|---|---|
| **Type** | The execution context: `Object` for actions tied to monitored infrastructure objects, `Automaton` for actions that interact with other automaton instances directly |
| **System file** | The path to the implementation file that the platform uses to run the action at runtime |

If the system file is not found in the current repository, the designer displays a warning. The file may exist in the platform's runtime environment and the automaton will still function correctly.

!!! info
    The classes and system files available for use in actions depend on the platform deployment. For a list of standard implementations, contact the XAUTOMATA delivery team.

---

## The warning indicator

If the Action section is present but not yet configured, the section header displays a **⚠** indicator. This is a visual reminder that the state has an action slot that has not been filled in. It does not prevent the automaton from being saved or committed.
