# Creating a Transition

Transitions connect states and define the conditions under which the automaton moves from one state to another.

---

## Adding a transition

Click the **recycle icon** in the left sidebar to enter transition creation mode. Then draw a connection between two states on the canvas.

The configuration panel opens on the right, showing the **Metadata** section with the input and output states pre-filled.

The transition is created immediately. Its configuration can be adjusted in the panel at any time.

---

## Setting input and output states

In the **Metadata** section, use the **Input State** and **Output State** dropdowns to set the source and destination of the transition.

Both fields are required. The available options are all states defined in the current automaton.

!!! note
    A transition can have the same state as both input and output. This creates a **self-loop** — the automaton remains in that state until a different condition is met.

---

## Selecting the transition type

The type of transition is determined automatically based on the fields you fill in:

- If you set a **Target Automaton** in the Parameters section, the transition becomes a **TransitionNew** or **TransitionNewMulti**.
- Otherwise, it is a standard **Transition**.

For a full description of each type and when to use it, see [Transition Types](types.md).

---

## Deleting a transition

Select a transition on the canvas by clicking its edge or label, then click the **trash icon** in the top-right corner of the configuration panel.
