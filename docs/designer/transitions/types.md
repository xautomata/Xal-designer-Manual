# Transition Types

The type of a transition decides what it does when it fires and which fields its panel shows. The panel title shows the type.

You do not choose the type from a list: on a new transition it follows the fields you fill in (see [Creating a Transition](creating.md#the-transition-type)).

---

## Transition

The standard transition moves the automaton from the input state to the output state.

Its panel shows:

- **Metric Value**: the value the metric of the input state must return;
- **Ignore Min Wait**: lets the transition fire without waiting for the min wait (see below);
- **Clock Constraints** and **Clock Reset Variables**.

A transition without a Metric Value and without clock constraints fires as soon as the automaton is in the input state.

See [Metric Value and Clocks](metric_clocks.md) for these fields.

---

## TransitionNew

A TransitionNew starts a **child automaton** and moves the parent to the output state.

Its panel shows:

- **Target File** and **Target Automaton**: the automaton to start;
- **Target Min Wait**: the min wait of the child, in seconds (see below);
- the **Input Parameter** rows: the values passed to the child when it starts;
- **Clock Constraints** and **Clock Reset Variables**.

A TransitionNew has no Metric Value: it fires on its clock constraints alone, and the min wait never delays it.

See [Spawning Child Automata](transition_new.md) for the target and the parameters.

!!! info
    For how parent and child automata work together, see [Automaton Families](../../concepts/families.md).

---

## TransitionNewMulti

A TransitionNewMulti works like a TransitionNew, but starts **one child for each item of a list**. The list is a Global State of the parent, chosen in the loop row of **Input Parameter**.

---

## TransitionX

TransitionX is an older form that puts a transition and the start of a child in one element. The interpreter still runs it, and the designer draws it as a plain arrow, but **you cannot create one** from the designer.

To get the same behavior, draw a Transition and a TransitionNew between the same two states. At run time they become a single transition: it fires on the Metric Value of the Transition, and uses the clock constraints and resets of the TransitionNew.

---

## Min Wait and Ignore Min Wait

The **min wait** is the time, in seconds, an automaton must spend in a state before it can leave it. It is set by whoever starts the automaton: for a child automaton, it is the **Target Min Wait** of the TransitionNew that started it. With no min wait, or 0, nothing is delayed.

**Ignore Min Wait** exempts one transition from it: the transition can fire as soon as its conditions hold.

---

## Transition types on the canvas

| Appearance | Type |
|---|---|
| Solid arrow | Transition |
| Dashed arrow, no ✚ | Transition with **Ignore Min Wait** on |
| Dashed arrow, label **✚** and the target automaton | TransitionNew |
| Dashed arrow, label **✚✚** and the target automaton | TransitionNewMulti |
| Solid arrow, no label | TransitionX |

The label of a Transition shows its Metric Value, plus a clock symbol for a clock constraint and **↺** for a clock reset. Hover an arrow to see the details.
