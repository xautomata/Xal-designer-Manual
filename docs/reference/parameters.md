# Parameter Passing Reference

Parameters allow automata to exchange data — between a parent and a child at spawn time, and between an automaton and its metric or action functions.

---

## Parameter element

```xml
<Parameter LocalVariable="orderId" TargetVariable="orderId"/>
```

| Attribute | Required | Description |
|---|---|---|
| `LocalVariable` | Yes | The name of the variable in the current automaton's global state |
| `TargetVariable` | Yes | The name of the variable in the target context (child automaton or function) |
| `Position` | No | Used in some function types to specify the positional argument index |

---

## Input parameters

`<Input>` blocks pass values **into** a function or child automaton.

In a metric or action, input parameters provide context to the implementation:

```xml
<Action Id="getOrder" Type="object">
  <System Name="getOrderFromShopify" Class="ecommerce.ShopifyConnector" Path="/src/ShopifyConnector.java"/>
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</Action>
```

In a `TransitionNew`, input parameters pass values from the parent's global state to the child's global state at instantiation:

```xml
<TransitionNew IdInputState="newOrder" IdOutputState="instantiatedOperator"
  Path="ecommerce_backoffice.xal" Type="Operator">
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
    <Parameter LocalVariable="shopifyCredentials" TargetVariable="shopifyCredentials"/>
  </Input>
</TransitionNew>
```

---

## Output parameters

`<o>` blocks capture values **returned** by a metric or action and write them into the automaton's global state:

```xml
<Metric Id="checkPickingConfirmation" Type="object">
  <System Name="checkPickingConfirmation" Class="wms.WaterFallConnector" Path="/src/WaterFallConnector.java"/>
  <o>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </o>
</Metric>
```

After the metric executes, the value of `TargetVariable` returned by the function is written into the `LocalVariable` in the automaton's global state.

---

## Loop input parameters

`<LoopInput>` is used exclusively in `TransitionNewMulti`. It contains a single `<Parameter>` pointing to a list variable in the parent's global state. The transition iterates over the list and spawns one child instance per item:

```xml
<TransitionNewMulti IdInputState="Start" IdOutputState="Cycle"
  Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <LoopInput>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </LoopInput>
</TransitionNewMulti>
```

Each spawned child receives one element from the `AppPools` list as the value of its `AppPool` variable.

---

## Variable resolution

When the designer writes parameter values to XAL, variables that match a global state variable name are automatically prefixed with `this.GLOBALSTATE_`. This prefix is resolved by the platform runtime and is invisible in the designer interface.

For example, if the automaton has a global state variable named `DbHostName`, a parameter referencing it will appear in the XAL as:

```xml
<Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
```

But may be stored internally as `this.GLOBALSTATE_DbHostName` depending on the context.
