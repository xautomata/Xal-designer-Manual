# Parameter Passing Reference

Parameters allow automata to exchange data — between a parent and a child at spawn time, and between an automaton and its metric or action functions.

---

## Parameter element

```xml
<Parameter LocalVariable="orderId" TargetVariable="orderId"/>
```

| Attribute | Required | Description |
|---|---|---|
| `LocalVariable` | Yes | The name of the variable in the current automaton's global state, without any prefix |
| `TargetVariable` | Yes | The name of the variable in the target context: the variable of the child automaton, or the parameter of the Java method |
| `Position` | No | Not used by the interpreter: the arguments of a method follow the order of the `<Parameter>` elements |

---

## Input parameters

`<Input>` blocks pass values **into** a function or child automaton.

In a metric or action with `Type="object"`, input parameters become the arguments of the Java method. The interpreter passes them **by position**, in the order of the `<Parameter>` elements: the order must match the signature of the method. `TargetVariable` holds the name of the method parameter, for readability. If the function has a `<Query>` or a `<DataSource>`, their values come first (see [Actions](actions.md#query)).

```xml
<Action Id="getOrder" Type="object">
  <System Name="getOrderFromShopify" Class="ecommerce.ShopifyConnector" Path="/src/ShopifyConnector.java"/>
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</Action>
```

In a `TransitionNew`, `TransitionNewMulti` or a function with `Type="automaton"`, input parameters copy values from the parent's global state to the child's global state at instantiation. The value of `LocalVariable` in the parent becomes the starting value of `TargetVariable` in the child:

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

`<Output>` blocks capture values **returned** by a metric or action and write them into the automaton's global state:

```xml
<Metric Id="checkPickingConfirmation" Type="object">
  <System Name="checkPickingConfirmation" Class="wms.WaterFallConnector" Path="/src/WaterFallConnector.java"/>
  <Output>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </Output>
</Metric>
```

With `Type="object"`:

- with one output parameter, the value returned by the method is written into its `LocalVariable`;
- with more than one, the method returns a map whose keys are the `LocalVariable` names, and each value is written into the matching variable.

With `Type="automaton"`, when the child finishes, the value of each `TargetVariable` in the child is written into the matching `LocalVariable` of the parent.

For the value that drives the transitions of a metric, see [Metrics](metrics.md#output).

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

In a `<Parameter>`, `LocalVariable` is always the plain name of a global state variable, for example `DbHostName`:

```xml
<Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
```

The `this.GLOBALSTATE_` prefix is used elsewhere, where the XAL expects a value (see [Referencing variables](global_state_clocks.md#referencing-variables)).

---

## Special input values

Instead of a global state variable, `LocalVariable` can name one of these values, prepared by the interpreter:

| `LocalVariable` | Type | Value |
|---|---|---|
| `XAL_EXCEPTION_DESCRIPTION` | `string` | The detail of the last error raised by an action or a metric of the instance |
| `XAL_SELF_DESCRIPTION` | `string` | The description of the current instance |
| `XAL_GLOBAL_STATE_VARS` | `map[string,string]` | All the global state variables of the instance, with their values as text |

`TargetVariable` gives the name the value takes in the child automaton or in the method.
