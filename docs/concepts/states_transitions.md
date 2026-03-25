# States and Transitions

States and transitions are the building blocks of every automaton. Understanding how they work together is essential before using the XAL Designer.

---

## States

A state represents a condition or phase in the lifecycle of the monitored process.

Every automaton has exactly one **initial state** — the state in which it begins when first instantiated. It may also have one or more **final states** — states that mark the end of the automaton's lifecycle. When a final state is reached, the automaton stops processing.

All other states are intermediate states, representing the active phases of the process.

In the XAL Designer, states are displayed as colored nodes on the canvas:

| Color | Meaning |
|---|---|
| Green | Initial state |
| Red | Final state |
| Blue | Intermediate state |

Each state can optionally be associated with a **metric** or an **action**. When the automaton enters a state that has a metric associated, it begins observing that metric. When it enters a state with an action, it executes that action.

---

## Transitions

A transition defines the condition under which the automaton moves from one state to another.

Every transition has:

- an **input state** — the state the automaton must be in
- an **output state** — the state the automaton moves to
- optionally, a **metric value** — the specific value that triggers the transition
- optionally, one or more **clock constraints** — time-based conditions that must be satisfied

When the automaton is in the input state and all transition conditions are met, the transition fires and the automaton moves to the output state.

---

## Transition types

The XAL Designer supports four types of transitions.

**Transition** is the standard type. It fires when a specific metric value is observed, when a clock constraint is met, or unconditionally if no conditions are defined.

**TransitionNew** spawns a new child automaton when it fires. The parent automaton continues in its output state while the child runs independently. This is how automaton families are built.

**TransitionNewMulti** works like TransitionNew but spawns multiple child instances at once, one for each item in a loop variable.

**TransitionX** is an extended transition type that combines features of the above. It is used for advanced scenarios.

---

## Self-loops

A transition can have the same state as both its input and output. This is called a **self-loop**. Self-loops are commonly used to keep the automaton in a waiting state, polling a metric until a condition changes.

---

## The Ignore Min Wait option

Each transition has a minimum wait time that prevents it from firing too rapidly. The **Ignore Min Wait** option bypasses this wait, allowing the transition to fire immediately. It is typically enabled when a transition should react instantly to a change.

!!! info
    The exact behavior of Min Wait and its interaction with Ignore Min Wait in specific scenarios is tracked as an open question. See `qa.md` — Q4.
