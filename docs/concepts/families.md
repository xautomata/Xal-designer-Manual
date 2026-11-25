# Automaton Families

Complex processes are rarely modeled with a single automaton. Instead, they are composed of multiple automata that work together — a **family**.

---

## Parent and child automata

In an automaton family, one automaton acts as the **orchestrator**. It manages the overall process flow and delegates specific tasks to **child automata** that it spawns at runtime.

Each child automaton has its own states, transitions, and lifecycle. A parent can start a child in two ways:

- with a **TransitionNew**: the child runs on its own, and the parent follows it by reading its state;
- with an **action or a metric of type Automaton**: the child runs as a step of the parent, which waits for it to finish.

This separation keeps each automaton focused on a single responsibility, making the overall system easier to understand, maintain, and reuse.

---

## Spawning child automata

A parent automaton spawns a child using a **TransitionNew** transition. When this transition fires, a new instance of the specified automaton is created and begins running immediately. The parent moves to the output state of the transition and goes on with its own work.

The parent can pass data to the child at the moment of spawning, through input parameters. For example, a parent managing an order process might pass the order ID and API credentials to the child automaton responsible for creating the order in an ERP system.

A **TransitionNewMulti** starts one child for each item of a list, for example one per database to relocate.

Once started, the child is not linked to its parent: it does not report back, and it keeps running even if the parent finishes first. It shares the parent's **group label**, which lets the parent find it. See [Spawning Child Automata](../designer/transitions/transition_new.md) for the fields.

---

## How the parent monitors a child

Once a child is running, the parent monitors its progress through a **metric**. The metric reads the child's global state — specifically, its current status — using a SQL-like query on the child's table, filtered by the group label they share.

The parent transitions based on the value returned by this metric. If the child reaches a success state, the parent advances to the next step. If the child reaches an error state, the parent can retry, escalate, or abort.

---

## Running an automaton as a step

An action or a metric can run another automaton instead of a Java method (see [Type Automaton](../designer/states/actions.md#type-automaton)). In this case:

1. the child starts with the values set in **Action Sign** (or **Metric Sign**);
2. the parent stays in its state and does nothing else until the child reaches a final state;
3. the child's output values are copied into the parent's Global States, as set in **Action Return** (or **Metric Return**). For a metric, the value marked as the symbol becomes the metric value, and chooses the parent's next transition.

Use it when the parent cannot go on without the child's result. In the Process Overview, this link is drawn as a [Family Tree](../designer/overview_graph/correlation_types.md#family-tree) correlation.

---

## Common patterns

**Starter / Operator** is a pattern used when a process must run once per event. A persistent automaton (the Starter) continuously monitors an external system for new events. When it detects one, it spawns an Operator automaton to handle that specific event. The Starter then returns to watching for the next event, while the Operator runs the full process lifecycle independently.

This pattern is common in integration scenarios — for example, spawning one Operator per incoming order from an e-commerce platform.

**Coordinator / Worker** is a pattern where an orchestrator automaton manages a pool of worker automata, each responsible for a single item. The coordinator spawns the workers, usually with a TransitionNewMulti, monitors their progress, and aggregates their results. This pattern is used when the same operation must be performed on multiple items in parallel.

---

## Automata across repositories

The platform does not see repositories: it sees the XAL files it loads. Every automaton of a loaded file gets its own table, and a query can read the table of any of them, whatever file or repository it comes from (see [Query Syntax](../reference/queries.md#table-naming-convention)).

So automata in different repositories can communicate, as long as the platform loads both files. If the file of the target automaton is not loaded, there is no table to read. The same applies to the automaton started by a TransitionNew: its file must be loaded too.

In the Process Overview of the XAL Designer, an automaton that is referenced but not defined in the current repository is drawn as a cloud (see [Reading the Process Overview](../designer/overview_graph/reading.md#node-shapes)).
