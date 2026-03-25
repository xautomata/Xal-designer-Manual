# Actions and Metrics

Actions and metrics are the two types of functions that connect an automaton to the outside world. They are defined at the automaton level and associated with individual states.

---

## Metrics

A metric is a function that **reads data** from a monitored system and returns a value.

When an automaton enters a state associated with a metric, it begins observing that metric. The value returned by the metric is then compared against the metric values defined on the outgoing transitions of that state — and the matching transition fires.

Metrics typically read the operational state of another automaton or a monitored infrastructure object. They do this through a query, written in a SQL-like syntax, that retrieves the current global state of the target.

A metric also defines the set of values it can return, called its **enumeration**. Each value in the enumeration corresponds to a possible outcome — and typically to a transition that handles that outcome.

---

## Actions

An action is a function that **performs an operation** on an external system.

When an automaton enters a state associated with an action, it executes that action. Actions can interact with external systems such as ERP platforms, warehouse management systems, ticketing tools, or custom APIs.

Unlike metrics, actions do not return a value that drives transitions directly. The transition out of an action state is determined by the action's outcome, which is handled separately.

---

## How metrics and actions work together

In practice, a state often has both a metric and an action associated. The action performs work, and the metric monitors the result until a completion or error condition is detected.

For example, in an order processing automaton:

- An action state sends a picking list to a warehouse management system.
- The automaton then moves to a waiting state where a metric continuously checks whether the WMS has confirmed the picking.
- When the metric returns `confirmed`, the transition fires and the process continues.

---

## The query mechanism

Metrics use a SQL-like query to read the state of another automaton. The query targets a virtual table that represents the global state of a specific automaton instance, identified by a group label.

The group label is a binding mechanism that ties the query to the correct instance of the target automaton — the one running on the same monitored object as the querying automaton.

!!! info
    The technical details of the query syntax and the group label binding are covered in the [XAL Reference — Query Syntax](../reference/queries.md) section.

---

## Data types

Both metrics and actions can pass data between automata using **input** and **output** parameters. Input parameters provide values to the function from the automaton's global state. Output parameters write results back into the global state after the function completes.

This mechanism allows automata to share context — for example, passing an order ID from a parent automaton to a child automaton when it is spawned.
