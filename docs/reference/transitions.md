# Transitions Reference

Transitions define how the automaton moves between states. Four transition types are available, each with its own element name and set of attributes.

---

## Common attributes

All transition types share these attributes:

| Attribute | Required | Description |
|---|---|---|
| `IdInputState` | Yes | ID of the state the automaton must be in for the transition to fire |
| `IdOutputState` | Yes | ID of the state the automaton moves to when the transition fires |
| `IgnoreMinWait` | No | If `true`, bypasses the minimum wait interval between successive firings |

---

## Transition

The standard transition. Fires when a metric value matches and/or clock constraints are satisfied.

```xml
<Transition
  IdInputState="Waiting"
  IdOutputState="Processing"
  MetricValue="confirmed"
  IgnoreMinWait="true">
  <ClockConstraint ClockExp="clk_wait &lt; T_max_wait"/>
  <ClockReset ClockVar="clk_wait"/>
</Transition>
```

Additional attributes:

| Attribute | Description |
|---|---|
| `MetricValue` | The metric return value that triggers the transition |

Child elements:

| Element | Description |
|---|---|
| `<ClockConstraint ClockExp="..."/>` | A time-based condition. Multiple elements allowed. |
| `<ClockReset ClockVar="..."/>` | Resets the specified clock when the transition fires. Multiple elements allowed. |

---

## TransitionNew

Spawns a child automaton and moves to the output state. Used to build automaton families.

```xml
<TransitionNew
  IdInputState="Init"
  IdOutputState="Processing"
  Path="ecommerce_backoffice.xal"
  Type="Operator"
  MinWait="300"
  IgnoreMinWait="true">
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</TransitionNew>
```

Additional attributes:

| Attribute | Required | Description |
|---|---|---|
| `Path` | Yes | File name of the XAL file containing the target automaton |
| `Type` | Yes | ID of the automaton to instantiate |
| `MinWait` | No | Minimum delay in seconds before the child begins processing |

Child elements:

| Element | Description |
|---|---|
| `<Input><Parameter .../></Input>` | Maps parent global state variables to child global state variables |
| `<ClockConstraint>`, `<ClockReset>` | Same as `Transition` |

---

## TransitionNewMulti

Like `TransitionNew`, but iterates over a list variable and spawns one child instance per item.

```xml
<TransitionNewMulti
  IdInputState="Init"
  IdOutputState="Running"
  Path="patcher.xal"
  Type="RecycleAppPools"
  MinWait="300">
  <LoopInput>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </LoopInput>
  <Input>
    <Parameter LocalVariable="erpOrderId" TargetVariable="orderId"/>
  </Input>
</TransitionNewMulti>
```

Additional child elements:

| Element | Description |
|---|---|
| `<LoopInput><Parameter .../></LoopInput>` | A single list variable from the parent's global state. One child is spawned per item. |

---

## TransitionX

An extended transition that combines features of `Transition` and `TransitionNew`. It supports both a `MetricValue` condition and the ability to spawn a child automaton through a `<New>` child element.

```xml
<TransitionX
  IdInputState="Active"
  IdOutputState="Handling"
  MetricValue="alert">
  <New Path="handlers.xal" Type="AlertHandler" MinWait="300"/>
  <ClockReset ClockVar="clk_alert"/>
</TransitionX>
```

Additional child elements:

| Element | Description |
|---|---|
| `<New Path="..." Type="..." MinWait="...">` | Spawns a child automaton. Supports `<Input>` and `<LoopInput>` as children. |

---

## Using `<` and `>` in clock expressions

XML requires angle brackets to be escaped inside attribute values. Use `&lt;` for `<` and `&gt;` for `>` in `ClockExp` expressions:

```xml
<ClockConstraint ClockExp="clk_retry &lt; T_max_wait"/>
<ClockConstraint ClockExp="clk_auth &gt;= T_auth_timeout"/>
```
