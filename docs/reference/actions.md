# Actions Reference

Actions are defined within the `<Functions>` element and represent operations the automaton performs when it enters a state associated with that action.

---

## Action element

```xml
<Action Id="createOrderInERP" Type="object" MinWait="0">
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
| `Type` | Yes | Execution context: `object` for actions tied to monitored infrastructure objects, `automaton` for actions interacting with other automaton instances. |
| `MinWait` | No | Minimum interval in milliseconds between successive executions of this action. |

---

## Child elements

### `<System>`

Specifies the runtime implementation of the action.

| Attribute | Description |
|---|---|
| `Name` | The method or function name to invoke |
| `Class` | The fully qualified class name containing the implementation |
| `Path` | The file path of the implementation, relative to the repository root |

### `<Query>`

An optional SQL-like expression providing context data before the action executes. Used by some action types to retrieve state from the platform database.

### `<Input>`

Maps global state variables from the automaton into the action's input parameters.

```xml
<Input>
  <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
</Input>
```

### `<Output>`

Maps the action's return values back into the automaton's global state.

```xml
<Output>
  <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
</Output>
```

### `<Async>`

Marks the action as asynchronous and sets a timeout:

```xml
<Async TimeoutSec="30"/>
```

---

## Notes

- Both `<Input>` and `<Output>` are optional. An action that has no side effects on the automaton's global state may omit them.
- The `Class` and `Path` in `<System>` refer to implementations available in the platform's runtime environment. The designer warns if the file is not found in the current repository, but the automaton can still function if the class exists at runtime.

!!! info
    For information on which system classes are available in your deployment, contact the XAUTOMATA delivery team. See also `qa.md` — Q15.
