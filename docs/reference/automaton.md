# Automaton Reference

The `<Automaton>` element is the top-level container for a single automaton definition within an XAL file.

---

## Attributes

| Attribute | Required | Description |
|---|---|---|
| `Id` | Yes | Unique identifier of the automaton within the file. Used by `TransitionNew` elements and metric queries to reference this automaton. |

---

## Child elements

| Element | Required | Description |
|---|---|---|
| `<Functions>` | Yes | Contains `<Action>` and `<Metric>` definitions. Must be present even if empty. |
| `<States>` | Yes | Contains all `<State>` definitions. |
| `<InitialState>` | Yes | Declares the starting state using `IdState`. |
| `<Transitions>` | Yes | Contains all transition elements. |
| `<GlobalState>` | No | Contains `<Variable>` definitions for shared state. |
| `<Clocks>` | No | Contains `<Variable>` definitions for clock timers. |
| `<FinalStates>` | No | Contains `<FinalState>` elements declaring terminal states. |

---

## Minimal example

An automaton with two states and one transition:

```xml
<Automaton Id="SimpleMonitor">
  <Functions/>
  <GlobalState>
    <Variable Name="status" Type="string"/>
  </GlobalState>
  <Clocks/>
  <States>
    <State Id="Watching"/>
    <State Id="Alerted"/>
  </States>
  <InitialState IdState="Watching"/>
  <FinalStates>
    <FinalState IdState="Alerted"/>
  </FinalStates>
  <Transitions>
    <Transition
      IdInputState="Watching"
      IdOutputState="Alerted"
      MetricValue="critical"
      IgnoreMinWait="true"/>
  </Transitions>
</Automaton>
```

---

## Multiple automata in one file

A single XAL file commonly contains a family of related automata. The parent automaton references children by their `Id` in `TransitionNew` elements, and children reference the parent through metric queries.

```xml
<XAL>
  <Automaton Id="Parent">
    ...
    <Transitions>
      <TransitionNew
        IdInputState="Init"
        IdOutputState="Running"
        Path="myfile.xal"
        Type="Child"
        MinWait="300"/>
    </Transitions>
  </Automaton>

  <Automaton Id="Child">
    ...
  </Automaton>
</XAL>
```

All automaton IDs within the same file must be unique.
