# Transitions Reference

Transitions define how the automaton moves between states. Four transition types are available, each with its own element name and set of attributes.

---

## Common attributes

All transition types share these attributes:

| Attribute | Required | Description |
|---|---|---|
| `IdInputState` | Yes | ID of the state the automaton must be in for the transition to fire |
| `IdOutputState` | Yes | ID of the state the automaton moves to when the transition fires |
| `IgnoreMinWait` | No | If `true`, the transition can fire without waiting for the min wait of the automaton. See [Min Wait and Ignore Min Wait](../designer/transitions/types.md#min-wait-and-ignore-min-wait). |

---

## Transition

The standard transition. Fires when a metric value matches and/or clock constraints are satisfied.

```xml
<Transition
  IdInputState="Waiting"
  IdOutputState="Processing"
  MetricValue="confirmed"
  IgnoreMinWait="true">
  <ClockConstraint ClockExp="clk_wait &lt; this.GLOBALSTATE_maxWait"/>
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
| `<ClockConstraint ClockExp="..."/>` | A time-based condition. Multiple elements allowed: all must hold. See [Clock constraint syntax](global_state_clocks.md#clock-constraint-syntax). |
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
| `Path` | Yes | File name of the XAL file containing the target automaton. It can be the same file. |
| `Type` | Yes | ID of the automaton to instantiate |
| `MinWait` | No | The min wait of the child, in seconds: the minimum time it stays in each of its states. See [Min Wait and Ignore Min Wait](../designer/transitions/types.md#min-wait-and-ignore-min-wait). |

`Path` and `Type` can also take the value of a global state variable, written as `this.GLOBALSTATE_<name>`: the file and the automaton are read from that variable when the transition fires.

Child elements:

| Element | Description |
|---|---|
| `<Input><Parameter .../></Input>` | Maps parent global state variables to child global state variables. See [Parameter Passing](parameters.md#input-parameters). |
| `<ClockConstraint>`, `<ClockReset>` | Same as `Transition` |

A `TransitionNew` has no `MetricValue`. When it is the only transition between two states, it always ignores the min wait of the parent, whatever `IgnoreMinWait` says. When a `Transition` joins the same two states, the interpreter merges them into one transition: the `MetricValue` and `IgnoreMinWait` come from the `Transition`, the clock constraints and resets from the `TransitionNew`. See [Transition Types](../designer/transitions/types.md).

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
| `<LoopInput><Parameter .../></LoopInput>` | Required. A single list variable from the parent's global state. One child is spawned per item. See [Loop input parameters](parameters.md#loop-input-parameters). |

---

## TransitionX

An older form that combines features of `Transition` and `TransitionNew` in one element. It supports both a `MetricValue` condition and the ability to spawn a child automaton through a `<New>` child element. The interpreter still runs it, and the XAL Designer reads it, but you cannot create one from the designer (see [Transition Types](../designer/transitions/types.md#transitionx)).

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
<ClockConstraint ClockExp="clk_retry &lt; 10 min"/>
<ClockConstraint ClockExp="clk_auth &gt;= this.GLOBALSTATE_authTimeout"/>
```
