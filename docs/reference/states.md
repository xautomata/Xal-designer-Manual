# States Reference

States are defined within the `<States>` element and represent the phases of the automaton's lifecycle.

---

## State element

```xml
<State
  Id="Waiting"
  IdMetric="checkStatus"
  IdAction="sendNotification"
  Starred="true"
  style='{"x":120,"y":-340}'/>
```

### Attributes

| Attribute | Required | Description |
|---|---|---|
| `Id` | Yes | Unique identifier of the state within the automaton. Referenced by transitions and the initial/final state declarations. |
| `IdMetric` | No | ID of the `<Metric>` function to observe while the automaton is in this state. |
| `IdAction` | No | ID of the `<Action>` function to execute when the automaton enters this state. |
| `Starred` | No | If `true`, marks the state as starred. See [Starred states](#starred-states). |
| `style` | No | JSON object storing the canvas position of the node (`x`, `y`). Managed automatically by the designer — do not edit manually. |

A state can have both `IdMetric` and `IdAction` defined simultaneously.

---

## Initial state

Exactly one state must be declared as the initial state:

```xml
<InitialState IdState="Init"/>
```

The `IdState` attribute must match the `Id` of an existing state in the automaton.

---

## Final states

Terminal states are declared in the `<FinalStates>` element:

```xml
<FinalStates>
  <FinalState IdState="Completed"/>
  <FinalState IdState="Failed"/>
  <FinalState IdState="Exception"/>
</FinalStates>
```

An automaton stops processing when it reaches any of its final states. Final states typically have no outgoing transitions.

---

## State color coding in the designer

| Color | State type |
|---|---|
| Green | Initial state |
| Bordeaux | Final state |
| Blue | Intermediate state |

---

## Starred states

A starred state marks a phase that the platform must know about. When the platform's process queue is configured, at every cycle the interpreter sends to it the instances that are in a starred state, with their data.

The `Starred` attribute is set through the ★ toggle in the state configuration panel of the XAL Designer (see [Creating a State](../designer/states/creating.md)).

!!! note
    `Starred` on a global state variable is a different thing: it adds the value of the variable to the interpreter's log messages about the instance (see [Global State](global_state_clocks.md#variable-attributes)).
