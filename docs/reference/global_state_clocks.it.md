# Riferimento Global State e Clocks

---

## Global State

L'elemento `<GlobalState>` dichiara variabili che persistono per l'intero ciclo di vita dell'istanza dell'automa. Queste variabili costituiscono la memoria di lavoro dell'automa.

```xml
<GlobalState>
  <Variable Name="orderId" Type="string"/>
  <Variable Name="retryCount" Type="int" Value="0"/>
  <Variable Name="shopifyApiEndpoint" Type="string" Desc="Shopify API endpoint"/>
</GlobalState>
```

### Attributi di Variable

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Name` | Sì | Identificatore utilizzato per referenziare la variabile nell'automa |
| `Type` | No | Suggerimento sul tipo di dato. Valori comuni: `string`, `int`, `boolean`, `list`, `object`, `ArrayList`, `map[string,int]` |
| `Value` | No | Valore predefinito assegnato quando l'automa viene istanziato |
| `Desc` | No | Descrizione leggibile. Usata solo come documentazione. |
| `IO` | No | Indica se la variabile è un input (`I`), output (`O`), o entrambi (`I/O`) ai fini del passaggio di parametri |
| `Starred` | No | Contrassegna la variabile come starred. Il comportamento dipende dalla piattaforma. |

### Referenziare le variabili

Le variabili del global state sono referenziate per nome in:

- gli elementi `<Parameter LocalVariable="...">` nei blocchi Input e Output
- le espressioni ClockConstraint quando una variabile memorizza un valore soglia
- la selezione della destinazione in `TransitionNew` quando `Type` referenzia una variabile del global state

Quando il designer serializza un riferimento a una variabile che corrisponde al nome di una variabile del global state, aggiunge automaticamente il prefisso `this.GLOBALSTATE_` al valore nell'output XAL. Questo prefisso è gestito in modo trasparente — non è necessario digitarlo manualmente nel designer.

---

## Clocks

L'elemento `<Clocks>` dichiara variabili timer utilizzate nelle condizioni di transizione.

```xml
<Clocks>
  <Variable Name="clk_retry"/>
  <Variable Name="clk_handshake"/>
</Clocks>
```

### Attributi delle variabili Clock

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Name` | Sì | Identificatore utilizzato negli elementi `ClockConstraint` e `ClockReset` |

I clock non hanno tipo, valore o descrizione — sono puri timer. Un clock inizia a contare da zero quando viene resettato da un elemento `<ClockReset>` su una transizione.

### Sintassi di ClockConstraint

```xml
<ClockConstraint ClockExp="clk_retry &lt; T_max_wait"/>
<ClockConstraint ClockExp="clk_auth &gt;= T_auth_timeout"/>
```

L'attributo `ClockExp` contiene l'espressione di confronto. Usare gli operatori con escape XML:

| Operatore | Encoding XML |
|---|---|
| `<` | `&lt;` |
| `<=` | `&lt;=` |
| `>` | `&gt;` |
| `>=` | `&gt;=` |

Il lato destro dell'espressione è tipicamente una costante nominata (es. `T_max_wait`) il cui valore è configurato a livello di piattaforma, o un numero letterale con un suffisso di unità (es. `5 min`, `30 min`).

### Reset del Clock

```xml
<ClockReset ClockVar="clk_retry"/>
```

Resetta il clock specificato a zero quando la transizione scatta. Su una singola transizione possono apparire più elementi `<ClockReset>`.
