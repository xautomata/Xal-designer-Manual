# Riferimento States

Gli States sono definiti all'interno dell'elemento `<States>` e rappresentano le fasi del ciclo di vita dell'automa.

---

## Elemento State

```xml
<State
  Id="Waiting"
  IdMetric="checkStatus"
  IdAction="sendNotification"
  Starred="true"
  style='{"x":120,"y":-340}'/>
```

### Attributi

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Id` | Sì | Identificatore univoco dello stato all'interno dell'automa. Referenziato dalle transizioni e dalle dichiarazioni di stato iniziale/finale. |
| `IdMetric` | No | ID della funzione `<Metric>` da osservare mentre l'automa si trova in questo stato. |
| `IdAction` | No | ID della funzione `<Action>` da eseguire quando l'automa entra in questo stato. |
| `Starred` | No | Se `true`, contrassegna lo stato come starred. Usato come marcatore visivo nel XAL Designer. |
| `style` | No | Oggetto JSON che memorizza la posizione del nodo sul canvas (`x`, `y`). Gestito automaticamente dal designer — non modificare manualmente. |

Uno stato può avere sia `IdMetric` che `IdAction` definiti simultaneamente.

---

## Stato iniziale

Esattamente uno stato deve essere dichiarato come stato iniziale:

```xml
<InitialState IdState="Init"/>
```

L'attributo `IdState` deve corrispondere all'`Id` di uno stato esistente nell'automa.

---

## Stati finali

Gli stati terminali sono dichiarati nell'elemento `<FinalStates>`:

```xml
<FinalStates>
  <FinalState IdState="Completed"/>
  <FinalState IdState="Failed"/>
  <FinalState IdState="Exception"/>
</FinalStates>
```

Un automa smette di elaborare quando raggiunge uno qualsiasi dei suoi stati finali. Gli stati finali tipicamente non hanno transizioni uscenti.

---

## Codifica dei colori degli stati nel designer

| Colore | Tipo di stato |
|---|---|
| Bordo verde | Stato iniziale |
| Bordo e riempimento rosso/bordeaux | Stato finale |
| Testo blu, riempimento grigio | Stato intermedio |

---

## Stati starred

L'attributo `Starred` viene impostato tramite l'interruttore ★ nel pannello di configurazione dello stato del XAL Designer. Il suo comportamento a runtime dipende dalla piattaforma.

!!! info
    L'uso esatto di `Starred` a runtime è tracciato come una domanda aperta. Vedere `qa.md` — Q1.
