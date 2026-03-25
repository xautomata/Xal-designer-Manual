# Opening an Automaton

You can open an automaton for editing from two places: the Process Overview graph and the Repository Explorer.

---

## From the Process Overview

The Overview tab displays the correlation graph of all automata in the repository.

**Double-click** on any node in the graph to open the corresponding automaton on the canvas. It appears as a new tab in the tab bar.

Single-clicking a node only highlights its connections — it does not open the automaton. Make sure to double-click.

!!! note
    Nodes representing automata in external repositories (shown with a red border) cannot be opened directly from the Overview, as their source files are not part of the current repository.

---

## From the Repository Explorer

Click the **file icon** in the left sidebar to open the Repository Explorer.

The explorer shows the folder structure of the connected repository. XAL files can be expanded to reveal the automata they contain. Each automaton is listed below its file with a robot icon.

Click an automaton name to open it on the canvas.

![Repository Explorer with automaton list](../../images/designer/overview_graph/repo_explorer_expanded.png)
/// caption
Fig.1 - An expanded XAL file in the Repository Explorer showing its automata
///

---

## Working with multiple automata

Each automaton you open appears as a separate tab. You can have several automata open at the same time and switch between them freely by clicking their tabs.

Opening the same automaton a second time switches focus to its existing tab rather than opening a duplicate.

---

## Navigating back to the Overview

Click the **Overview** tab at any time to return to the correlation graph. The automaton tabs remain open in the background — your work is not lost.
