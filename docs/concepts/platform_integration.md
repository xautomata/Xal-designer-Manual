# How Automata Relate to the Platform

Automata do not operate in isolation. They are deeply integrated with the XAUTOMATA platform and interact with its entities, monitoring data, and operational workflows.

---

## Automata and monitored objects

Every automaton instance is associated with one or more monitored objects in the platform — servers, network devices, services, or any other infrastructure resource.

The automaton reads metric data produced by those objects and reacts to changes in their operational state. This is what makes the automaton a **digital twin** of the monitored system: it mirrors the real-world behavior of the infrastructure in a structured, queryable form.

---

## Automata and dispatchers

When an automaton transitions between states, it can trigger a **dispatcher**. A dispatcher is an operational rule that connects a state transition to an external action — sending a notification, opening a ticket, or calling an API.

Dispatchers can themselves be implemented as automata. In this case, a dedicated child automaton monitors the parent's state and handles all outgoing communications, keeping the main process logic clean.

---

## Automata and metrics in the platform

The metrics observed by automata are the same metrics collected by the platform's monitoring probes. When a probe detects a change in an infrastructure object, that change flows into the metric, and the automaton reacts accordingly.

This connection means that automata respond to real infrastructure events in near-real time, without requiring manual intervention.

---

## The XAL Designer in context

The XAL Designer is the tool used to create and modify automata. It connects to a Git repository containing `.xal` files and provides a visual canvas for editing automaton logic.

Changes made in the designer are committed back to the repository through a standard Git workflow. The platform then picks up the updated files and applies them to the running automaton instances.

The overall flow is:

1. Define or modify automaton logic in the XAL Designer.
2. Commit and push changes to the repository.
3. The platform validates and deploys the updated automata.
4. Running instances begin applying the new logic.

!!! note
    The XAUTOMATA platform validates all XAL files at load time. A file that does not pass validation will not be loaded, regardless of what the designer displays.
