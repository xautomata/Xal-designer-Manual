# What is an Automaton

An automaton is the core processing unit of the XAUTOMATA platform. It defines the operational logic applied to a monitored system — what to observe, how to react, and when to trigger actions.

---

## The basic idea

An automaton models the behavior of a real-world system as a sequence of **states** connected by **transitions**.

At any given moment, an automaton is in exactly one state. When a specific condition is met — such as a metric reaching a certain value, or a time threshold being exceeded — the automaton moves from one state to another. This movement is called a **transition**.

Each state can be associated with a **metric** to observe or an **action** to execute. Metrics read data from the monitored system. Actions perform operations on it or on external systems.

---

## A concrete example

Consider a server being monitored for CPU usage.

An automaton for this server might have three states: **Normal**, **Warning**, and **Critical**. When the CPU metric reports a value above 80%, the automaton transitions from Normal to Warning. When the value exceeds 95%, it transitions to Critical and executes an action — for example, opening a support ticket.

When the CPU returns to an acceptable level, the automaton transitions back to Normal.

This logic runs continuously, reacting to real conditions as they change.

---

## One automaton, many objects

A single automaton definition can run simultaneously across many monitored objects. The same CPU monitoring logic applies to every server in the infrastructure — each server gets its own running instance of the automaton, tracking its own state independently.

This is what makes automata scalable: you define the logic once, and the platform applies it wherever it is needed.

---

## Automata working together

Complex processes are often modeled as **families of automata** that collaborate. A parent automaton orchestrates the overall process and spawns child automata to handle specific sub-tasks. Each child runs independently, completes its work, and reports its final state back to the parent.

For example, an IT patching process might involve a main automaton that coordinates the overall patching sequence, and separate child automata responsible for relocating databases, recycling application pools, and sending notifications at each step.

This compositional approach allows complex operational logic to be broken into manageable, reusable pieces.

---

## How automata relate to the platform

Automata do not run in isolation. They are connected to the XAUTOMATA platform through:

- **Metrics** — data collected from monitored infrastructure objects and services
- **Actions** — operations performed on external systems such as ticketing platforms, APIs, or notification channels
- **Dispatchers** — rules that trigger notifications and external workflows based on automaton state transitions

The **XAL Designer** is the tool used to create and edit automata visually, without writing XML by hand.

!!! note
    For a detailed description of the XAL file format and all available elements, see the [XAL Reference](../reference/file_structure.md) section.
