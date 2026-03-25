# Transition Types

The XAL Designer supports multiple transition types. The type determines what the transition does when it fires and what configuration fields are available.

---

## Transition

The standard transition moves the automaton from one state to another when a condition is met.

It supports:

- a **Metric Value** — the specific value returned by the input state's metric that triggers the transition
- **Clock Constraints** — time-based conditions that must also be satisfied
- **Clock Reset Variables** — clocks to reset when the transition fires
- **Ignore Min Wait** — bypasses the minimum wait time, allowing the transition to fire immediately

If no conditions are configured, the transition fires unconditionally as soon as the automaton enters the input state.

On the canvas, standard transitions are displayed as **solid arrows**.

---

## TransitionNew

A TransitionNew spawns a new child automaton when it fires, in addition to moving the automaton to the output state.

It supports:

- **Target Automaton** — the automaton to instantiate (selected from the automata defined in the current file, or specified as a global state variable)
- **Target Min Wait** — a minimum wait time before the child automaton begins processing
- **Input Parameters** — values passed from the parent's global state to the child at the moment of instantiation
- **Clock Constraints** and **Clock Reset Variables**

On the canvas, TransitionNew edges are displayed as **dashed arrows**, and the label shows the name of the spawned automaton prefixed with a **+** symbol.

!!! info
    For a conceptual explanation of how parent and child automata interact, see [Automaton Families](../../concepts/families.md).

---

## TransitionNewMulti

TransitionNewMulti works like TransitionNew but spawns multiple child instances in a single operation, iterating over a list variable.

It requires a **Loop Input** parameter in addition to the standard input parameters — a single variable from the global state that contains the list of items to iterate over.

---

## TransitionX

TransitionX is an extended transition type that combines features from all other types. It is used for advanced scenarios that require both spawning a child automaton and matching a metric value on the same transition.

---

## Identifying transition types on the canvas

| Appearance | Type |
|---|---|
| Solid arrow | Transition |
| Dashed arrow with **+** label | TransitionNew or TransitionNewMulti |
| Label showing metric value | Any type with a Metric Value condition |
| Label showing clock expression | Any type with a Clock Constraint |
