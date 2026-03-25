# Automaton Families

Complex processes are rarely modeled with a single automaton. Instead, they are composed of multiple automata that work together — a **family**.

---

## Parent and child automata

In an automaton family, one automaton acts as the **orchestrator**. It manages the overall process flow and delegates specific tasks to **child automata** that it spawns at runtime.

Each child automaton runs independently. It has its own states, transitions, and lifecycle. When it reaches a final state, the parent automaton reads its outcome through a metric and decides what to do next.

This separation keeps each automaton focused on a single responsibility, making the overall system easier to understand, maintain, and reuse.

---

## Spawning child automata

A parent automaton spawns a child using a **TransitionNew** transition. When this transition fires, a new instance of the specified automaton is created and begins running immediately.

The parent can pass data to the child at the moment of spawning, through input parameters. For example, a parent managing an order process might pass the order ID and API credentials to the child automaton responsible for creating the order in an ERP system.

---

## How the parent monitors a child

Once a child is running, the parent monitors its progress through a **metric**. The metric reads the child's global state — specifically, its current status — using a SQL-like query.

The parent transitions based on the value returned by this metric. If the child reaches a success state, the parent advances to the next step. If the child reaches an error state, the parent can retry, escalate, or abort.

---

## Common patterns

**Starter / Operator** is a pattern used when a process must run once per event. A persistent automaton (the Starter) continuously monitors an external system for new events. When it detects one, it spawns an Operator automaton to handle that specific event. The Starter then returns to watching for the next event, while the Operator runs the full process lifecycle independently.

This pattern is common in integration scenarios — for example, spawning one Operator per incoming order from an e-commerce platform.

**Coordinator / Worker** is a pattern where an orchestrator automaton manages a pool of worker automata, each responsible for a single item. The coordinator spawns workers, monitors their progress, and aggregates their results. This pattern is used when the same operation must be performed on multiple items in parallel.

---

## Automata across repositories

Automata in different repositories can communicate at runtime. One automaton can read the state of another through the metric query mechanism, even if they are defined in separate files or repositories.

In the Process Overview of the XAL Designer, references to automata that exist outside the current repository are shown as distinct nodes, visually differentiated from automata defined within the repository.

!!! info
    The technical mechanism behind cross-repository communication is tracked as an open question. See `qa.md` — Q18.
