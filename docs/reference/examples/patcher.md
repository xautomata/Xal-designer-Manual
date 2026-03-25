# Annotated Example — IT Patcher

This example illustrates an **orchestrated patching workflow** with multiple operating modes, clock-based waiting, and a parallel observer automaton for notifications.

The file `patcher.xal` models the automated patching of an Oracle database cluster, including database relocation, instance shutdown, OS update, reboot, and service restart.

---

## Automata in this file

| Automaton | Role |
|---|---|
| `Patcher` | Main orchestrator — manages the full patching sequence |
| `RelocateDb` | Moves database instances off the node before patching |
| `RelocateBackDb` | Moves database instances back after patching |
| `AppPoolsManager` | Iterates over a list of application pools and recycles each one |
| `RecycleAppPools` | Recycles a single application pool |
| `Dispatcher` | Observer — monitors the Patcher's state and sends notifications |

---

## Patcher

The `Patcher` automaton starts by selecting one of three operating modes:

```xml
<State Id="selectScenario" IdMetric="getScenario"/>
```

```xml
<Transition IdInputState="selectScenario" IdOutputState="balanced"    MetricValue="balanced"/>
<Transition IdInputState="selectScenario" IdOutputState="human"        MetricValue="human"/>
<Transition IdInputState="selectScenario" IdOutputState="maintenanceInterval" MetricValue="maintenance"/>
```

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

A single clock (`waitClock`) is reused throughout the automaton, reset at each step that requires a fresh timer.

### Exception handling

The `exception` state monitors the Dispatcher automaton's liveness:

```xml
<State Id="exception" IdMetric="mEXCEPTION"/>
```

```xml
<Transition IdInputState="exception" IdOutputState="exception"
  MetricValue="dispatchingAlive">
  <ClockConstraint ClockExp="waitClock &gt;= 5 min"/>
</Transition>
<Transition IdInputState="exception" IdOutputState="finished"
  MetricValue="dispatchingDied"/>
```

If the Dispatcher is still alive, the Patcher waits 5 minutes and retries. If the Dispatcher has stopped, the Patcher terminates cleanly. This ensures the notification system has a chance to report the failure before the process exits.

---

## RelocateDb

`RelocateDb` iterates over database instances, relocating each one to a different node before patching begins. It then spawns `AppPoolsManager` to recycle application pools.

```xml
<TransitionNew IdInputState="removeFromBalancer" IdOutputState="RecycleAppPools"
  Path="patcher.xal" Type="AppPoolsManager" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPools"/>
  </Input>
</TransitionNew>
```

The list of application pools is passed as an input parameter to `AppPoolsManager`.

---

## AppPoolsManager and RecycleAppPools

`AppPoolsManager` implements a loop over a list of application pools using `TransitionNewMulti`'s pattern manually — it spawns one `RecycleAppPools` child per pool, cycling through the list:

```xml
<TransitionNew IdInputState="Start" IdOutputState="Cycle"
  Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </Input>
</TransitionNew>
```

Each `RecycleAppPools` instance handles one pool, classifying it and applying the appropriate restart strategy.

---

## Dispatcher

The `Dispatcher` automaton is a **parallel observer**. It is spawned by the Patcher at the very beginning:

```xml
<TransitionNew IdInputState="initPatch" IdOutputState="selectScenario"
  Path="patcher.xal" Type="Dispatcher" MinWait="600"/>
```

It continuously polls the Patcher's global state and sends a notification whenever the Patcher changes phase:

```xml
<State Id="polling" IdMetric="getAutomataStatus"/>
<State Id="notifyInit"     IdAction="notify" IdMetric="getNotificationStatus"/>
<State Id="notifyPatching" IdAction="notify" IdMetric="getNotificationStatus"/>
```

When the Patcher finishes or enters an exception, the Dispatcher sends a final notification and terminates. The Patcher monitors whether the Dispatcher is still alive through its exception-handling logic.

---

## Key patterns illustrated

- **Multi-mode orchestration**: branching at startup to select the operating mode
- **Clock-based delays**: using `ClockConstraint` to enforce wait times between steps
- **Parallel observer**: a dedicated child automaton that monitors and reports on the parent
- **Mutual monitoring**: parent and child watching each other's liveness
- **List iteration**: `AppPoolsManager` cycling through a list by repeatedly spawning and waiting for child completions
- **Parameter passing**: credentials and lists passed down through the automaton hierarchy
