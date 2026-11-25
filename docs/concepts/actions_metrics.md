# Actions and Metrics

Actions and metrics are the two types of functions that connect an automaton to the outside world. They are defined at the automaton level and associated with individual states.

---

## Metrics

A metric is a function that **reads data** from a monitored system and returns a value.

When an automaton enters a state associated with a metric, it runs the metric once. The value returned by the metric is then compared against the metric values defined on the outgoing transitions of that state — and the matching transition fires. To read the metric again, the automaton must enter the state again, usually through a self-loop.

Many metrics read the operational state of another automaton. They do this through a query, written in a SQL-like syntax, that retrieves the current global state of the target.

A metric also defines the set of values it can return, called its **enumeration**. Each value in the enumeration corresponds to a possible outcome — and typically to a transition that handles that outcome.

---

## Actions

An action is a function that **performs an operation** on an external system.

When an automaton enters a state associated with an action, it executes that action. Actions can interact with external systems such as ERP platforms, warehouse management systems, ticketing tools, or custom APIs.

An action does not choose the next transition: only the metric value does. An action can write its results into the Global State through its output parameters. A state with an action and no metric leaves through a transition without a Metric Value.

---

## How metrics and actions work together

A state can have both an action and a metric. The action runs first, then the metric reads the result.

The metric runs only once per entry, so a process that must wait for an external system usually splits the work in two states: one that performs the action, and one that checks the result with a metric and a self-loop until a completion or error condition is detected.

For example, in an order processing automaton:

- An action state sends a picking list to a warehouse management system.
- The automaton then moves to a waiting state where a metric checks whether the WMS has confirmed the picking, and a self-loop repeats the check until it has.
- When the metric returns `confirmed`, the transition fires and the process continues.

---

## The query mechanism

Metrics use a SQL-like query to read the state of another automaton. The query targets a virtual table that represents the global state of a specific automaton instance, identified by a group label.

The group label is a binding mechanism that ties the query to the correct instance of the target automaton — the one running on the same monitored object as the querying automaton.

!!! info
    The technical details of the query syntax and the group label binding are covered in the [XAL Reference — Query Syntax](../reference/queries.md) section.

---

## Input and output parameters

Both metrics and actions exchange data with the automaton through **input** and **output** parameters. Input parameters provide values to the function from the automaton's global state. Output parameters write results back into the global state after the function completes.

To pass data to a child automaton when it starts, for example an order ID, use the input parameters of the [TransitionNew](../designer/transitions/transition_new.md#input-parameters) instead.

---

## Functions that run an automaton

An action or a metric usually runs a Java method. It can also run **another automaton**: the calling automaton waits in the state until the called one finishes, then receives its output values. See [Automaton Families](families.md#running-an-automaton-as-a-step).

---

## When a function fails

If the method reports a temporary error, the platform runs it again every minute, up to ten times. After that, or after a fatal error, the automaton instance stops and waits for a manual fix.
