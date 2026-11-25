# Actions Reference

Actions are defined within the `<Functions>` element and represent operations the automaton performs when it enters a state associated with that action.

---

## Action element

```xml
<Action Id="createOrderInERP" Type="object">
  <System
    Name="createOrderInERP"
    Class="erp.TeamSystemConnector"
    Path="/erp/src/TeamSystemConnector.java"/>
  <Input>
    <Parameter LocalVariable="erpApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderData" TargetVariable="orderData"/>
  </Input>
  <Output>
    <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
  </Output>
</Action>
```

---

## Attributes

| Attribute | Required | Description |
|---|---|---|
| `Id` | Yes | Unique identifier within the `<Functions>` element. Referenced by state `IdAction` attributes. |
| `Type` | Yes | What the action runs: `object` for a Java method, `automaton` for another automaton. See [Type automaton](#type-automaton). |
| `MinWait` | No | Only with `Type="automaton"`: the min wait, in seconds, of the automaton the action starts. See [Min Wait and Ignore Min Wait](../designer/transitions/types.md#min-wait-and-ignore-min-wait). The interpreter ignores it on `object` actions. |

---

## Child elements

### `<System>`

Specifies what the action runs.

| Attribute | `Type="object"` | `Type="automaton"` |
|---|---|---|
| `Name` | The name of the method | The `Id` of the automaton to start |
| `Class` | The fully qualified name of the class | The name of the XAL file that contains the automaton |
| `Path` | The path of the `.java` file in the repository | Not used |

The method must be `public static`. The interpreter loads the class from its classpath, which includes the jar files listed in the `parameter` attribute of `<XAL>` (see [File Structure](file_structure.md#root-element)): the `.java` file in the repository is not required at runtime.

### `<Query>`

An optional query on the state of other automata. The interpreter runs it before the action and passes the result to the method as its first argument, a `list[string]`. See [Query Syntax](queries.md).

### `<DataSource>`

An optional reference to a data source of the platform, by its code. The interpreter passes the data source to the method as an argument, after the query result.

```xml
<DataSource Code="erp-production"/>
```

### `<Input>`

Lists the global state variables passed to the action. See [Parameter Passing](parameters.md#input-parameters).

```xml
<Input>
  <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
</Input>
```

### `<Output>`

Lists the global state variables where the action writes its result. See [Parameter Passing](parameters.md#output-parameters).

```xml
<Output>
  <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
</Output>
```

---

## Type automaton

With `Type="automaton"` the action starts another automaton as a step of the current one: the current automaton waits for it to finish (see [Automaton Families](../concepts/families.md)).

```xml
<Action Id="relocate" Type="automaton" MinWait="60">
  <System Name="RelocateDb" Class="patcher.xal"/>
  <Input>
    <Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
  </Input>
  <Output>
    <Parameter LocalVariable="relocationResult" TargetVariable="result"/>
  </Output>
</Action>
```

`Name` and `Class` can also take the value of a global state variable, written as `this.GLOBALSTATE_<name>`: the automaton and the file are read from that variable when the action runs.

---

## Notes

- Both `<Input>` and `<Output>` are optional. An action that has no side effects on the automaton's global state may omit them.
- The designer warns if the `.java` file is not found in the current repository, but the automaton can still work if the class exists at runtime.

!!! info
    For information on which system classes are available in your deployment, contact the XAUTOMATA delivery team.
