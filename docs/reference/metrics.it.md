# Riferimento Metrics

Le Metric sono definite all'interno dell'elemento `<Functions>` e rappresentano funzioni di lettura dati che l'automa utilizza per osservare lo stato di altri automi o oggetti monitorati.

---

## Elemento Metric

```xml
<Metric Id="checkPickingConfirmation" Type="object">
  <System
    Name="checkPickingConfirmation"
    Class="wms.WaterFallConnector"
    Path="/wms/src/WaterFallConnector.java"/>
  <Query>
    SELECT GLOBALSTATE_STATUS
    FROM scenario_FollowerManager
    WHERE groupLabel = 'this.GROUPLABEL'
  </Query>
  <Input>
    <Parameter LocalVariable="wmsPickingListId" TargetVariable="pickingListId"/>
  </Input>
  <Output>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </Output>
  <Enumeration>
    <Const Value="confirmed"/>
    <Const Value="pending"/>
    <Const Value="error"/>
  </Enumeration>
</Metric>
```

---

## Attributi

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Id` | Sì | Identificatore univoco all'interno di `<Functions>`. Referenziato dagli attributi `IdMetric` degli stati. |
| `Type` | No | Cosa esegue la metric: `object` per un metodo Java, `automaton` per un altro automa, come per le [action](actions.md#tipo-automaton). |
| `MinWait` | No | Solo con `Type="automaton"`: il min wait, in secondi, dell'automa che la metric avvia. Sulle metric `object` l'interprete lo ignora. |

---

## Elementi figlio

### `<System>`

Indica cosa esegue la metric, come per le action (vedi [Actions](actions.md#system)).

| Attributo | Descrizione |
|---|---|
| `Name` | Il nome del metodo da invocare (con `Type="automaton"`, l'automa da avviare) |
| `Class` | Il nome completo della classe (con `Type="automaton"`, il file XAL) |
| `Path` | Il percorso del file di implementazione |

### `<Query>`

Un'espressione simile a SQL che legge lo stato dell'automa o dell'oggetto di destinazione. Vedere [Query Syntax](queries.md) per la specifica completa.

### `<Input>`

Mappa le variabili del global state nei parametri di input della metric.

### `<Output>`

Mappa i valori di ritorno della metric nel global state dell'automa. L'attributo `MetricSymbol` su `<Output>` può opzionalmente specificare il nome della variabile usato per esporre il valore della metric esternamente.

### `<Enumeration>`

Definisce l'insieme dei valori che la metric può restituire. Ogni `<Const Value="..."/>` dichiara un possibile valore di ritorno.

```xml
<Enumeration>
  <Const Value="confirmed"/>
  <Const Value="pending"/>
  <Const Value="error"/>
</Enumeration>
```

I valori dell'Enumeration sono usati come condizioni `MetricValue` sulle transizioni uscenti dagli stati associati a questa metric. Scattano solo le transizioni il cui `MetricValue` corrisponde a una costante dichiarata.

---

## Metric vs Action

Sia le metric che le action usano `<System>`, `<Input>` e `<Output>`. La differenza principale è il loro scopo:

- Una **metric** legge lo stato e restituisce un valore dall'enumerazione, guidando le transizioni.
- Una **action** esegue un'operazione e può aggiornare le variabili del global state, ma non restituisce un valore dell'enumerazione che guida le transizioni direttamente.

Nel XAL Designer, le metric sono configurate nella sezione **Metric** del pannello dello stato, e le action nella sezione **Action**.
