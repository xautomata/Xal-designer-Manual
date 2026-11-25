# XAL File Structure

An XAL file is an XML document that contains one or more automaton definitions. All automata in a file share the same namespace and can reference each other by name.

---

## Root element

The root element of every XAL file is `<XAL>`. It contains one or more `<Automaton>` elements.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<XAL>
  <Automaton Id="MyAutomaton">
    ...
  </Automaton>
  <Automaton Id="AnotherAutomaton">
    ...
  </Automaton>
</XAL>
```

---

## Automaton structure

Each `<Automaton>` element contains the following child elements, all at the same level and in any order:

| Element | Required | Description |
|---|---|---|
| `<Functions>` | Yes | Defines the metrics and actions used by the automaton |
| `<States>` | Yes | Lists all states |
| `<InitialState>` | Yes | Declares the starting state |
| `<Transitions>` | Yes | Contains all transition definitions |
| `<GlobalState>` | No | Declares shared variables |
| `<Clocks>` | No | Declares clock variables |
| `<FinalStates>` | No | Lists terminal states |

---

## Order of elements and attributes

The order of elements and attributes has no effect on the interpreter. The XAL Designer always writes them in the same order, so that two versions of a file are easy to compare:

- inside `<Automaton>`: `GlobalState`, `Clocks`, `Functions`, `States`, `InitialState`, `FinalStates`, `Transitions`;
- inside `<Functions>`: all the actions, then all the metrics;
- inside `<Transitions>`: all the `Transition` elements, then `TransitionNew`, `TransitionNewMulti` and `TransitionX`.

When you save a file written by hand or by another tool, the designer rewrites it in this order. The content does not change.

---

## File naming and organization

XAL files use the `.xal` extension. Multiple automata are typically grouped in a single file when they form a family — a parent automaton and its related children.

Files are organized in a directory structure within the repository. `TransitionNew` elements reference child automata by file name (`Path`) and automaton ID (`Type`). See [Transitions](transitions.md#transitionnew).

---

## Validation

XAL files are validated by the XAUTOMATA platform at load time, against the schema of the XAL language. A file that does not pass validation is not loaded, and none of its automata is executed. Elements or attributes that the schema does not know, a missing required element or a reference to a state that does not exist all make the file invalid.

The XAL Designer performs backend validation as part of the commit process, before pushing changes to the repository. See [Commit and Push](../designer/publishing/commit.md) for details.
