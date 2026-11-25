# Annotated Example — IT Patcher

This example illustrates an **orchestrated patching workflow** with multiple operating modes, clock-based waiting, and a parallel observer automaton for notifications.

The file `patcher.xal` models the automated patching of an Oracle database cluster, including database relocation, instance shutdown, OS update, reboot, and service restart.

The snippets below come from that file, cut down to the attributes that matter for each point.

---

## Automata in this file

| Automaton | Role |
|---|---|
| `Patcher` | Main orchestrator — manages the full patching sequence |
| `RelocateDb` | Moves database instances off the node before patching |
| `RelocateBackDb` | Moves database instances back after patching |
| `AppPoolsManager` | Starts `RecycleAppPools` for the application pools, then loops or finishes |
| `RecycleAppPools` | Recycles a single application pool |
| `Dispatcher` | Observer — monitors the Patcher's state and sends notifications |

---

## Patcher

The `Patcher` automaton starts by selecting one of three operating modes:

```xml
<State Id="selectScenario" IdMetric="getScenario"/>
```

```xml
<Transition IdInputState="selectScenario" IdOutputState="balanced"
  IgnoreMinWait="true" MetricValue="balanced"/>
<Transition IdInputState="selectScenario" IdOutputState="human"
  IgnoreMinWait="true" MetricValue="human"/>
<Transition IdInputState="selectScenario" IdOutputState="maintenanceInterval"
  IgnoreMinWait="true" MetricValue="maintenanceInterval"/>
```

Each `MetricValue` is one of the values listed in the `Enumeration` of the `getScenario` metric.

- **balanced**: proceeds automatically, first relocating databases using `RelocateDb`
- **human**: waits for explicit human approval before proceeding
- **maintenanceInterval**: waits for a scheduled maintenance window to open

This branching pattern allows the same patching logic to run in different operational contexts without duplicating the workflow.

### Clock-based waiting

After the OS reboot, the Patcher waits 20 minutes before checking whether Oracle CRS has restarted:

```xml
<Transition IdInputState="reboot" IdOutputState="checkOracleCRSRunning"
  MetricValue="success">
  <ClockConstraint ClockExp="waitClock &gt;= 20 min"/>
</Transition>
```

The transition that enters `reboot` (`executeYumUpdate → reboot`) resets the clock, so the 20 minutes count from the start of the reboot. A single clock (`waitClock`) is reused throughout the automaton: each transition that starts a new wait resets it.

### Exception handling

The `exception` state monitors the Dispatcher automaton's liveness:

```xml
<State Id="exception" IdMetric="mEXCEPTION"/>
```

```xml
<Transition IdInputState="exception" IdOutputState="exception"
  IgnoreMinWait="true" MetricValue="dispatchingAlive">
  <ClockConstraint ClockExp="waitClock &gt;= 5 min"/>
</Transition>
<Transition IdInputState="exception" IdOutputState="finished"
  IgnoreMinWait="true" MetricValue="dispatchingDied"/>
```

If the Dispatcher is still alive, the Patcher waits 5 minutes and retries. If the Dispatcher has stopped, the Patcher terminates cleanly. This ensures the notification system has a chance to report the failure before the process exits.

---

## RelocateDb

In `balanced` mode the Patcher starts `RelocateDb` and passes it the host to free:

```xml
<TransitionNew IdInputState="balanced" IdOutputState="checkRelocation"
  IgnoreMinWait="true" Path="patcher.xal" Type="RelocateDb" MinWait="300">
  <ClockReset ClockVar="waitClock"/>
  <Input>
    <Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
  </Input>
</TransitionNew>
```

`Type` is the `Id` of the child automaton, with the same upper and lower case.

The TransitionNew does not wait for the child: the Patcher moves straight to `checkRelocation`, and from there reads the state of `RelocateDb` through a metric until the relocation is over. See [How the parent monitors a child](../../concepts/families.md#how-the-parent-monitors-a-child).

`RelocateDb` moves the database instances off the node one at a time (`selectNextDB → findNode → relocate`), then takes the node out of the balancer and starts `AppPoolsManager` with the list of application pools:

```xml
<TransitionNew IdInputState="removeFromBalancer" IdOutputState="RecycleAppPools"
  IgnoreMinWait="true" Path="patcher.xal" Type="AppPoolsManager" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPools"/>
  </Input>
</TransitionNew>
```

---

## AppPoolsManager and RecycleAppPools

`AppPoolsManager` starts a `RecycleAppPools` in `Start` and moves to `Cycle`, where it either goes round again (`next`, back to `Start`), stops (`end`) or fails (`error`):

```xml
<TransitionNew IdInputState="Start" IdOutputState="Cycle"
  IgnoreMinWait="true" Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </Input>
</TransitionNew>
```

Each `RecycleAppPools` instance classifies the pool (balanced, ICRIF, batch) and applies the matching restart strategy.

!!! tip
    When the list is known in advance, a [TransitionNewMulti](../transitions.md#transitionnewmulti) starts one child for each item in a single step, without the loop.

---

## Dispatcher

The `Dispatcher` automaton is a **parallel observer**. The Patcher starts it as its very first step:

```xml
<TransitionNew IdInputState="initPatch" IdOutputState="selectScenario"
  IgnoreMinWait="true" Path="patcher.xal" Type="Dispatcher" MinWait="600"/>
```

It polls the status of the Patcher and sends a notification whenever the Patcher changes phase:

```xml
<State Id="polling" IdMetric="getAutomataStatus"/>
<State Id="notifyInit"     IdAction="notify" IdMetric="getNotificationStatus"/>
<State Id="notifyPatching" IdAction="notify" IdMetric="getNotificationStatus"/>
```

When the Patcher finishes or enters an exception, the Dispatcher sends a final notification and terminates. The Patcher monitors whether the Dispatcher is still alive through its exception-handling logic.

### The min wait as polling rate

`MinWait="600"` gives the Dispatcher a [min wait](../../designer/transitions/types.md#min-wait-and-ignore-min-wait) of 600 seconds: after each transition it stays at least 10 minutes in the new state, unless the transition that leaves has `IgnoreMinWait`. The `polling` loop does not have it, so the Dispatcher checks the Patcher every 10 minutes; the transitions to the notify states have it, so a notification goes out at once:

```xml
<Transition IdInputState="polling" IdOutputState="polling" MetricValue="waiting"/>
<Transition IdInputState="polling" IdOutputState="notifyPatching"
  IgnoreMinWait="true" MetricValue="patching"/>
```

---

## Key patterns illustrated

- **Multi-mode orchestration**: branching at startup to select the operating mode
- **Clock-based delays**: using `ClockConstraint` to enforce wait times between steps
- **Parallel observer**: a dedicated child automaton that monitors and reports on the parent
- **Mutual monitoring**: parent and child watching each other's liveness
- **Loop over children**: `AppPoolsManager` starting a child and deciding whether to go round again
- **Min wait as polling rate**: a child's min wait setting how often it checks, with `IgnoreMinWait` on the transitions that must not wait
- **Parameter passing**: the host name and the list of application pools passed down through the automaton hierarchy
