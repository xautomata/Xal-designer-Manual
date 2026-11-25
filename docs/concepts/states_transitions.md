# States and Transitions

States and transitions are the building blocks of every automaton. Understanding how they work together is essential before using the XAL Designer.

---

## States

A state represents a condition or phase in the lifecycle of the monitored process.

Every automaton has exactly one **initial state** — the state in which it begins when first instantiated. It may also have one or more **final states** — states that mark the end of the automaton's lifecycle. When a final state is reached, the automaton stops processing.

All other states are intermediate states, representing the active phases of the process.

In the XAL Designer, the color of the node border and of its left stripe shows the kind of state:

| Color | Meaning |
|---|---|
| Green | Initial state |
| Red | Final state |
| Blue | Intermediate state |

A state can have an **action**, a **metric**, or both. When the automaton enters the state, it runs the action first, then the metric, once each. To read the metric again, the automaton must enter the state again, for example through a [self-loop](#self-loops).

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

## Choosing the next transition

After running the action and the metric of a state, the automaton looks at the transitions that leave it:

- if **exactly one** transition is valid, it fires;
- if the metric value matches a transition but its clock constraints do not hold yet, the automaton waits in the state until they do;
- if **no** transition expects the metric value, or **two or more** are valid at the same moment, the automaton stops with an error.

Design each state so that every value the metric can return leads to exactly one transition.

---

## Transition types

The XAL Designer creates three types of transitions. A fourth one, TransitionX, can only be read.

**Transition** is the standard type. It fires when a specific metric value is observed, when a clock constraint is met, or unconditionally if no conditions are defined.

**TransitionNew** starts a child automaton when it fires. The parent moves to its output state, and the child runs on its own. This is how automaton families are built.

**TransitionNewMulti** works like TransitionNew, but starts one child for each item of a list held in a Global State of the parent.

**TransitionX** is an older form that combines a transition and the start of a child. The platform still runs it, but you cannot create one in the designer.

See [Transition Types](../designer/transitions/types.md) for how each type looks in the designer.

---

## Self-loops

A transition can have the same state as both its input and output. This is called a **self-loop**. Self-loops are commonly used to keep the automaton in a waiting state, polling a metric until a condition changes: each time the self-loop fires, the action and the metric of the state run again.

---

## Min Wait and Ignore Min Wait

The **min wait** is the time, in seconds, an automaton must spend in a state before it can leave it. It is set by whoever starts the automaton: for a child automaton, it is the **Target Min Wait** of the TransitionNew that started it. With no min wait, or 0, nothing is delayed.

The **Ignore Min Wait** option exempts one transition from it: the transition can fire as soon as its conditions hold. Enable it when a transition must react at once, for example to an error.
