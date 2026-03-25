# Metrics Reference

Metrics are defined within the `<Functions>` element and represent data-reading functions the automaton uses to observe the state of other automata or monitored objects.

---

## Metric element

```xml
<Metric Id="checkPickingConfirmation" Type="object" MinWait="0">
  <System
    Name="checkPickingConfirmation"
    Class="wms.WaterFallConnector"
    Path="/wms/src/WaterFallConnector.java"/>
  <Query>
    SELECT GLOBALSTATE_STATUS
    FROM Scenario_Follower_Manager
    WHERE groupLabel = 'this.GROUPLABEL'
  </Query>
  <Input>
    <Parameter LocalVariable="wmsPickingListId" TargetVariable="pickingListId"/>
  </Input>
  <Output>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </Output>
  <Enumeration>
    <Const Value="confirmed"/>
    <Const Value="pending"/>
    <Const Value="error"/>
  </Enumeration>
</Metric>
```

---

## Attributes

| Attribute | Required | Description |
|---|---|---|
| `Id` | Yes | Unique identifier within `<Functions>`. Referenced by state `IdMetric` attributes. |
| `Type` | No | Execution context: `object` for metrics tied to monitored objects, `automaton` for metrics reading other automaton instances directly. |
| `MinWait` | No | Minimum interval in milliseconds between successive evaluations of this metric. |

---

## Child elements

### `<System>`

Specifies the runtime implementation.

| Attribute | Description |
|---|---|
| `Name` | The method name to invoke |
| `Class` | The fully qualified class name |
| `Path` | The implementation file path |

### `<Query>`

A SQL-like expression that reads the target automaton's or object's state. See [Query Syntax](queries.md) for the full specification.

### `<Input>`

Maps global state variables into the metric's input parameters.

### `<Output>`

Maps the metric's return values back into the automaton's global state. The `MetricSymbol` attribute on `<Output>` can optionally specify the variable name used to expose the metric value externally.

### `<Enumeration>`

Defines the set of values the metric can return. Each `<Const Value="..."/>` declares one possible return value.

```xml
<Enumeration>
  <Const Value="confirmed"/>
  <Const Value="pending"/>
  <Const Value="error"/>
</Enumeration>
```

Enumeration values are used as `MetricValue` conditions on outgoing transitions from states associated with this metric. Only transitions whose `MetricValue` matches a declared constant will fire.

---

## Metric vs Action

Both metrics and actions use `<System>`, `<Input>`, and `<Output>`. The key difference is their purpose:

- A **metric** reads state and returns a value from the enumeration, driving transitions.
- An **action** performs an operation and may update global state variables, but does not return an enumeration value that drives transitions directly.

In the XAL Designer, metrics are configured in the **Metric** section of the state panel, and actions in the **Action** section.
