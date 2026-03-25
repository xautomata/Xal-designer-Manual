# Annotated Example — C2 Intercept

This example illustrates a **deep automaton hierarchy** with intensive clock usage, resource allocation logic, and a multi-phase mission workflow.

The file `c2_intercept.xal` models a command-and-control intercept scenario, where a mission proceeds through assessment, authorization, execution, and evaluation phases — each delegated to a specialized child automaton.

!!! note
    This file is a synthetic scenario designed to demonstrate advanced XAL capabilities. Its domain-specific terminology (threat, engagement, intercept) is used to illustrate complex operational patterns and does not reflect any specific real-world deployment.

---

## Automata in this file

| Automaton | Role |
|---|---|
| `Scenario` | Top-level orchestrator — sequences the mission phases |
| `Threat_Assessor` | Tracks and classifies the target |
| `Resource_Allocator` | Searches for available resources |
| `Engagement_Manager` | Handles authorization and monitors the active phase |
| `Radio_Link` | Monitors the communication link quality |
| `Mission_Evaluator` | Analyzes the outcome after execution |
| `Dispatcher` | Handles dispatch operations |

---

## Scenario

The `Scenario` automaton sequences the mission through five states, spawning a specialist child at each phase transition:

```
Init_intercept
  → (spawn Threat_Assessor, Dispatcher)
Assessing_threat
  → Threat-classified → (spawn Engagement_Manager)
Allocating_resources
  → Resources-assigned → (spawn Radio_Link)
Awaiting_authorization
  → Engagement-authorized → (spawn Mission_Evaluator)
Executing_intercept
  → Intercept-terminated
Evaluating_outcome
  → Evaluation-complete → Mission_complete / Allocating_resources
```

Each `TransitionNew` spawns a child and moves the parent forward. The parent then observes the child's state through a metric until a terminal value is reported.

---

## Threat_Assessor

The `Threat_Assessor` uses a clock to enforce a minimum tracking period before classification is allowed:

```xml
<Transition IdInputState="Init_assessment" IdOutputState="Tracking" IgnoreMinWait="true">
  <ClockReset ClockVar="clk_tracking"/>
</Transition>
<Transition IdInputState="Tracking" IdOutputState="Tracking" IgnoreMinWait="true">
  <ClockConstraint ClockExp="clk_tracking &lt; T_stable_contact"/>
</Transition>
<Transition IdInputState="Tracking" IdOutputState="Classifying">
  <ClockConstraint ClockExp="clk_tracking &gt;= T_stable_contact"/>
</Transition>
```

The automaton loops in `Tracking` until the clock exceeds the stability threshold, then proceeds to classification. This prevents premature decisions based on transient contacts.

---

## Resource_Allocator with retry logic

The `Scenario` handles resource unavailability using a clock-guarded self-loop on `Allocating_resources`:

```xml
<Transition IdInputState="Allocating_resources" IdOutputState="Allocating_resources"
  MetricValue="No-resources-available" IgnoreMinWait="true">
  <ClockConstraint ClockExp="t_wait_resources &lt; T_max_wait"/>
</Transition>
<Transition IdInputState="Allocating_resources" IdOutputState="Exception"
  MetricValue="Exception">
  <ClockConstraint ClockExp="t_wait_resources &gt;= T_max_wait"/>
</Transition>
```

If resources are unavailable, the Scenario keeps retrying until the maximum wait time is exceeded, at which point it transitions to `Exception`. This implements a bounded retry with timeout escalation.

---

## Engagement_Manager and Radio_Link

`Engagement_Manager` monitors the radio link quality through the `Radio_Link` child automaton. When the link is lost, it transitions to a recovery state:

```xml
<Transition IdInputState="Monitoring_approach" IdOutputState="Link_lost_management"
  MetricValue="Link-lost" IgnoreMinWait="true"/>
<Transition IdInputState="Link_lost_management" IdOutputState="Monitoring_approach"
  MetricValue="Link-active" IgnoreMinWait="true"/>
<Transition IdInputState="Link_lost_management" IdOutputState="Intercept_terminated">
  <ClockConstraint ClockExp="clk_link_recovery &gt;= T_link_timeout"/>
</Transition>
```

If the link recovers, the mission resumes from wherever it was interrupted. If the link remains lost beyond the timeout, the intercept is declared terminated regardless.

The `Radio_Link` automaton itself models signal degradation in three stages: `Link_active` → `Link_degraded` → `Link_lost`, each governed by separate clocks:

```xml
<Transition IdInputState="Link_active" IdOutputState="Link_degraded">
  <ClockConstraint ClockExp="clk_last_rx &gt;= T_rx_timeout_short"/>
  <ClockReset ClockVar="clk_degraded"/>
</Transition>
<Transition IdInputState="Link_degraded" IdOutputState="Link_lost">
  <ClockConstraint ClockExp="clk_degraded &gt;= T_rx_timeout_long"/>
  <ClockReset ClockVar="clk_lost"/>
</Transition>
```

---

## Mission_Evaluator with indeterminate outcome

The `Mission_Evaluator` handles cases where the outcome cannot be immediately determined:

```xml
<Transition IdInputState="Analyzing_outcome" IdOutputState="Outcome_indeterminate"
  IgnoreMinWait="true">
  <ClockReset ClockVar="clk_indeterminate"/>
</Transition>
<Transition IdInputState="Outcome_indeterminate" IdOutputState="Evaluation_complete">
  <ClockConstraint ClockExp="clk_indeterminate &gt;= T_indeterminate_timeout"/>
</Transition>
```

If the outcome remains indeterminate beyond the timeout, the evaluation is forced to complete. This prevents the mission from stalling indefinitely on an unresolved state.

---

## Key patterns illustrated

- **Sequential phase orchestration**: each phase spawned as a child, parent advancing on completion
- **Minimum observation period**: clock-enforced dwell time before state transitions are allowed
- **Bounded retry with timeout**: self-loop with clock constraint, escalating to exception on timeout
- **Graceful degradation**: link loss handled with recovery logic and a fallback timeout
- **Multi-stage signal modeling**: progressive degradation states with independent clocks
- **Forced resolution**: indeterminate states resolved by timeout to prevent stalling
