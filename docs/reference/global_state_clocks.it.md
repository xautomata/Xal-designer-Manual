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
| `Name` | Sì | Identificatore utilizzato per referenziare la variabile nell'automa. Unico nell'automa. |
| `Type` | No | Tipo di dato: `string`, `int`, `long`, `bool`, `object`, o una collezione come `list[string]` o `map[string,int]`. L'interprete lo usa per convertire `Value` e per far corrispondere i parametri dei metodi Java. |
| `Value` | No | Valore predefinito assegnato quando l'automa viene istanziato |
| `Desc` | No | Descrizione leggibile. Usata solo come documentazione. |
| `IO` | No | Indica se la variabile è un input (`I`), output (`O`), o entrambi (`I/O`). Solo documentazione: l'interprete non lo controlla nel passaggio di parametri. |
| `Starred` | No | Se `true`, l'interprete aggiunge il valore della variabile ai suoi messaggi di log sull'istanza, per esempio quando una funzione fallisce. |

### Referenziare le variabili

Le variabili del global state si referenziano in due modi:

- **Per nome**, nel `LocalVariable` di un `<Parameter>` nei blocchi Input, Output e LoopInput (vedi [Passaggio di parametri](parameters.md)).
- **Come `this.GLOBALSTATE_<nome>`**, dove l'XAL si aspetta un valore: nei vincoli dei clock, nelle query, in `Path` e `Type` di una `TransitionNew` o `TransitionNewMulti`, e in `Name` e `Class` del `<System>` di una funzione con `Type="automaton"`. L'interprete lo sostituisce con il valore corrente della variabile.

Nel designer scegli la variabile da un elenco e il prefisso `this.GLOBALSTATE_` lo aggiunge il designer.

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

I clock non hanno tipo, valore o descrizione — sono puri timer. Un clock inizia a contare da zero quando l'istanza parte, e di nuovo ogni volta che un elemento `<ClockReset>` su una transizione lo resetta.

Il nome `minWaitClockVariable` è riservato: l'interprete lo usa per il min wait e rifiuta un clock con quel nome.

### Sintassi di ClockConstraint

```xml
<ClockConstraint ClockExp="clk_retry &lt; 10 min"/>
<ClockConstraint ClockExp="clk_auth &gt;= this.GLOBALSTATE_authTimeout"/>
```

L'attributo `ClockExp` contiene l'espressione di confronto. Usare gli operatori con escape XML:

| Operatore | Encoding XML |
|---|---|
| `<` | `&lt;` |
| `<=` | `&lt;=` |
| `>` | `&gt;` |
| `>=` | `&gt;=` |

Il lato destro dell'espressione è uno di questi:

- un numero intero, seguito facoltativamente da un'unità: `sec`, `min`, `hour` o `day` (es. `5 min`, `2 hour`). Senza unità, il numero è in secondi;
- una variabile del global state, scritta come `this.GLOBALSTATE_<nome>`: il suo valore si legge quando il vincolo viene controllato, in secondi se non segue un'unità.

L'espressione può contenere solo clock dell'automa, variabili del global state, numeri interi, unità, gli operatori qui sopra e `+`, `-`, `*`, `/`. Qualsiasi altro nome, come una costante che non è né un clock né una variabile del global state, rende il vincolo non valido, e l'interprete segnala un errore nel suo log.

!!! note
    Il designer propone `sec`, `min` e `hour`. `day` è accettato dall'interprete, ma devi scriverlo a mano nell'XAL.

### Reset del Clock

```xml
<ClockReset ClockVar="clk_retry"/>
```

Resetta il clock specificato a zero quando la transizione scatta. Su una singola transizione possono apparire più elementi `<ClockReset>`.
