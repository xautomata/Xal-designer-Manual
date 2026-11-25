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
| `Starred` | No | Se `true`, contrassegna lo stato come starred. Vedi [Stati starred](#stati-starred). |
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
| Verde | Stato iniziale |
| Bordeaux | Stato finale |
| Blu | Stato intermedio |

---

## Stati starred

Uno stato starred segna una fase che la piattaforma deve conoscere. Quando la coda dei processi della piattaforma è configurata, a ogni ciclo l'interprete le invia le istanze che si trovano in uno stato starred, con i loro dati.

L'attributo `Starred` viene impostato tramite l'interruttore ★ nel pannello di configurazione dello stato del XAL Designer (vedi [Creare uno Stato](../designer/states/creating.md)).

!!! note
    `Starred` su una variabile di stato globale è un'altra cosa: aggiunge il valore della variabile ai messaggi di log dell'interprete sull'istanza (vedi [Global State](global_state_clocks.md#attributi-di-variable)).
