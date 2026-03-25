# Metric Value and Clocks

The **Metric Value** and **Clocks** fields in the transition panel control when a transition fires. They can be used independently or in combination.

---

## Metric Value

The **Metric Value** field specifies the value that the input state's metric must return for the transition to fire.

Select a value from the dropdown. The available options are drawn from the enumeration defined on the metric associated with the input state. If the input state has no metric, or its metric has no enumeration values, the dropdown will be empty.

If no Metric Value is set, the transition fires regardless of the metric's current value — as long as any clock constraints are also satisfied.

!!! warning
    Only one transition from the same input state can have the same combination of Metric Value and Clock Constraints. If you try to configure a duplicate, the panel will display a **"Metric value already in use"** error.

---

## Clock Constraints

The **Clocks** section lets you add time-based conditions to the transition.

To add a constraint, select a **Clock Variable**, an **Operator** (`<`, `<=`, `>`, `>=`), a **Value**, and a **Unit**. The constraint is displayed as an expression such as `clk_retry >= 5 min`.

A transition can have multiple clock constraints. All constraints must be satisfied simultaneously for the transition to fire.

To remove a constraint, click the **×** icon next to it.

---

## Clock Reset Variables

Use the **Clock Reset Variables** field to select one or more clocks that should be reset when the transition fires.

Click the dropdown to see all clocks defined for the current automaton and select the ones to reset. Each selected clock is shown as a tag that can be removed individually.

---

## Ignore Min Wait

The **Ignore Min Wait** toggle bypasses the minimum wait interval enforced between successive firings of the same transition. Enable it when the transition must react immediately to a change, without any delay.

---

## Combining conditions

Metric Value and Clock Constraints can be used together on the same transition. In that case, both conditions must be true simultaneously for the transition to fire — the metric must have returned the specified value, and all clock constraints must be satisfied.

This combination is commonly used to implement timeout escalation: one transition handles the normal case (metric value matches, clock within bounds), and another handles the timeout case (same metric value, clock exceeded).
