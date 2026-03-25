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
| `Name` | Yes | Identifier used to reference the variable throughout the automaton |
| `Type` | No | Data type hint. Common values: `string`, `int`, `boolean`, `list`, `object`, `ArrayList`, `map[string,int]` |
| `Value` | No | Default value assigned when the automaton is instantiated |
| `Desc` | No | Human-readable description. Used as documentation only. |
| `IO` | No | Indicates whether the variable is an input (`I`), output (`O`), or both (`I/O`) for parameter passing purposes |
| `Starred` | No | Marks the variable as starred. Behavior is platform-dependent. |

### Referencing variables

Global state variables are referenced by name in:

- `<Parameter LocalVariable="...">` elements in Input and Output blocks
- Clock constraint expressions when a variable stores a threshold value
- `TransitionNew` target selection when `Type` references a global state variable

When the designer serializes a variable reference that matches a global state variable name, it automatically prepends `this.GLOBALSTATE_` to the value in the XAL output. This prefix is handled transparently — you do not need to type it manually in the designer.

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

Clocks have no type, value, or description — they are pure timers. A clock starts counting from zero when it is reset by a `<ClockReset>` element on a transition.

### Clock constraint syntax

```xml
<ClockConstraint ClockExp="clk_retry &lt; T_max_wait"/>
<ClockConstraint ClockExp="clk_auth &gt;= T_auth_timeout"/>
```

The `ClockExp` attribute contains the comparison expression. Use XML-escaped operators:

| Operator | XML encoding |
|---|---|
| `<` | `&lt;` |
| `<=` | `&lt;=` |
| `>` | `&gt;` |
| `>=` | `&gt;=` |

The right-hand side of the expression is typically a named constant (e.g. `T_max_wait`) whose value is configured at the platform level, or a literal number with a unit suffix (e.g. `5 min`, `30 min`).

### Clock reset

```xml
<ClockReset ClockVar="clk_retry"/>
```

Resets the specified clock to zero when the transition fires. Multiple `<ClockReset>` elements can appear on a single transition.
