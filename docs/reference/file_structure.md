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

The optional `parameter` attribute on `<XAL>` specifies the base path used to resolve relative file references in `TransitionNew` elements.

---

## Automaton structure

Each `<Automaton>` element contains the following child elements, all at the same level:

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

## File naming and organization

XAL files use the `.xal` extension. Multiple automata are typically grouped in a single file when they form a family — a parent automaton and its related children.

Files are organized in a directory structure within the repository. `TransitionNew` elements reference child automata by file name and automaton ID, using a path relative to the current file's location.

---

## Validation

XAL files are validated by the XAUTOMATA platform at load time. A file that does not pass validation is not loaded, and its automata are not executed.

The XAL Designer performs backend validation as part of the commit process, before pushing changes to the repository. See [Commit and Push](../designer/publishing/commit.md) for details.
