# Managing Automata

The **Automata Management** panel provides tools for creating and organizing the automata within the currently loaded file.

To open it, click the **robot icon** in the left sidebar.

![Automata Management panel](../../images/designer/automata/management_panel.png)
/// caption
Fig.1 - The Automata Management panel
///

---

## Creating an automaton

Click **Create** to open the creation dialog.

![Create Automaton dialog](../../images/designer/automata/create_dialog.png)
/// caption
Fig.2 - The Create Automaton dialog
///

Fill in the following fields:

| Field | Description |
|---|---|
| **File** | The XAL file in which the automaton will be created. Pre-filled with the currently open file. Click the file icon to select a different one. |
| **Name** | The unique identifier of the automaton within the file. Required. |
| **Pattern** | An optional template to use as a starting point. *(Coming soon)* |

Click **Confirm** to create the automaton. It opens immediately as a new tab on the canvas with an empty diagram.

---

## Renaming an automaton

Click **Rename** to change the name of the currently active automaton.

!!! warning
    Renaming an automaton updates its identifier in the file. Any other automaton that references this one by name — through a `TransitionNew` or a metric query — will need to be updated manually.

---

## Deleting an automaton

Click **Delete** to remove the currently active automaton from the file.

!!! warning
    Deletion is immediate and cannot be undone within the designer. Commit your changes before deleting if you want to preserve the ability to restore the automaton from version control.

---

## Family Trees and Correlations

The **Family Trees** and **Correlations** options in the panel are not yet active.

When available, they will provide filtered views of the Process Overview focused respectively on parent-child relationships and on the full correlation graph of the selected automaton.
