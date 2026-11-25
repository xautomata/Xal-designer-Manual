# Global State and Clocks Reference

---

## Global State

The `<GlobalState>` element declares variables that persist throughout the entire lifecycle of the automaton instance. These variables form the automaton's working memory.

```xml
<GlobalState>
  <Variable Name="orderId" Type="string"/>
  <Variable Name="retryCount" Type="int" Value="0"/>
  <Variable Name="shopifyApiEndpoint" Type="string" Desc="Shopify API endpoint"/>
</GlobalState>
```

### Variable attributes

| Attribute | Required | Description |
|---|---|---|
| `Name` | Yes | Identifier used to reference the variable throughout the automaton. Unique within the automaton. |
| `Type` | No | Data type: `string`, `int`, `long`, `bool`, `object`, or a collection such as `list[string]` or `map[string,int]`. The interpreter uses it to convert `Value` and to match the parameters of Java methods. |
| `Value` | No | Default value assigned when the automaton is instantiated |
| `Desc` | No | Human-readable description. Used as documentation only. |
| `IO` | No | Marks the variable as an input (`I`), output (`O`), or both (`I/O`). Documentation only: the interpreter does not check it when passing parameters. |
| `Starred` | No | If `true`, the interpreter adds the value of the variable to its log messages about the instance, for example when a function fails. |

### Referencing variables

Global state variables are referenced in two ways:

- **By name**, in the `LocalVariable` of a `<Parameter>` in Input, Output and LoopInput blocks (see [Parameter Passing](parameters.md)).
- **As `this.GLOBALSTATE_<name>`**, where the XAL expects a value: in clock constraints, in queries, in `Path` and `Type` of a `TransitionNew` or `TransitionNewMulti`, and in `Name` and `Class` of the `<System>` of a function with `Type="automaton"`. The interpreter replaces it with the current value of the variable.

In the designer, you choose the variable from a list and the designer adds the `this.GLOBALSTATE_` prefix for you.

---

## Clocks

The `<Clocks>` element declares timer variables used in transition conditions.

```xml
<Clocks>
  <Variable Name="clk_retry"/>
  <Variable Name="clk_handshake"/>
</Clocks>
```

### Clock variable attributes

| Attribute | Required | Description |
|---|---|---|
| `Name` | Yes | Identifier used in `ClockConstraint` and `ClockReset` elements |

Clocks have no type, value, or description — they are pure timers. A clock starts counting from zero when the instance starts, and again every time a `<ClockReset>` element on a transition resets it.

The name `minWaitClockVariable` is reserved: the interpreter uses it for the min wait, and rejects a clock with that name.

### Clock constraint syntax

```xml
<ClockConstraint ClockExp="clk_retry &lt; 10 min"/>
<ClockConstraint ClockExp="clk_auth &gt;= this.GLOBALSTATE_authTimeout"/>
```

The `ClockExp` attribute contains the comparison expression. Use XML-escaped operators:

| Operator | XML encoding |
|---|---|
| `<` | `&lt;` |
| `<=` | `&lt;=` |
| `>` | `&gt;` |
| `>=` | `&gt;=` |

The right-hand side of the expression is one of:

- a whole number, optionally followed by a unit: `sec`, `min`, `hour` or `day` (e.g. `5 min`, `2 hour`). Without a unit, the number is in seconds;
- a global state variable, written as `this.GLOBALSTATE_<name>`: its value is read when the constraint is checked, in seconds unless a unit follows.

The expression can contain only clocks of the automaton, global state variables, whole numbers, units, the operators above and `+`, `-`, `*`, `/`. Any other name, such as a constant that is not a clock or a global state variable, makes the constraint invalid, and the interpreter reports an error in its log.

!!! note
    The designer offers `sec`, `min` and `hour`. `day` is accepted by the interpreter, but you have to write it by hand in the XAL.

### Clock reset

```xml
<ClockReset ClockVar="clk_retry"/>
```

Resets the specified clock to zero when the transition fires. Multiple `<ClockReset>` elements can appear on a single transition.
