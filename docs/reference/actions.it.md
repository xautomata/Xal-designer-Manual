# Riferimento Actions

Le Actions sono definite all'interno dell'elemento `<Functions>` e rappresentano le operazioni che l'automa esegue quando entra in uno stato associato a quella action.

---

## Elemento Action

```xml
<Action Id="createOrderInERP" Type="object">
  <System
    Name="createOrderInERP"
    Class="erp.TeamSystemConnector"
    Path="/erp/src/TeamSystemConnector.java"/>
  <Input>
    <Parameter LocalVariable="erpApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderData" TargetVariable="orderData"/>
  </Input>
  <Output>
    <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
  </Output>
</Action>
```

---

## Attributi

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Id` | Sì | Identificatore univoco all'interno dell'elemento `<Functions>`. Referenziato dagli attributi `IdAction` degli stati. |
| `Type` | Sì | Cosa esegue la action: `object` per un metodo Java, `automaton` per un altro automa. Vedi [Tipo automaton](#tipo-automaton). |
| `MinWait` | No | Solo con `Type="automaton"`: il min wait, in secondi, dell'automa che la action avvia. Vedi [Min Wait e Ignore Min Wait](../designer/transitions/types.md#min-wait-e-ignore-min-wait). Sulle action `object` l'interprete lo ignora. |

---

## Elementi figlio

### `<System>`

Indica cosa esegue la action.

| Attributo | `Type="object"` | `Type="automaton"` |
|---|---|---|
| `Name` | Il nome del metodo | L'`Id` dell'automa da avviare |
| `Class` | Il nome completo della classe | Il nome del file XAL che contiene l'automa |
| `Path` | Il percorso del file `.java` nel repository | Non usato |

Il metodo deve essere `public static`. L'interprete carica la classe dai file `.jar` installati sulla piattaforma: a runtime il file `.java` nel repository non serve.

### `<Query>`

Una query opzionale sullo stato di altri automi. L'interprete la esegue prima della action e ne passa il risultato al metodo come primo argomento, un `list[string]`. Vedi [Sintassi delle query](queries.md).

### `<DataSource>`

Un riferimento opzionale a una sorgente dati della piattaforma, tramite il suo codice. L'interprete passa la sorgente dati al metodo come argomento, dopo il risultato della query.

```xml
<DataSource Code="erp-production"/>
```

### `<Input>`

Elenca le variabili del global state passate alla action. Vedi [Passaggio di parametri](parameters.md#parametri-di-input).

```xml
<Input>
  <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
</Input>
```

### `<Output>`

Elenca le variabili del global state in cui la action scrive il suo risultato. Vedi [Passaggio di parametri](parameters.md#parametri-di-output).

```xml
<Output>
  <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
</Output>
```

---

## Tipo automaton

Con `Type="automaton"` la action avvia un altro automa come passo di quello corrente: l'automa corrente aspetta che finisca (vedi [Famiglie di automata](../concepts/families.md)).

```xml
<Action Id="relocate" Type="automaton" MinWait="60">
  <System Name="RelocateDb" Class="patcher.xal"/>
  <Input>
    <Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
  </Input>
  <Output>
    <Parameter LocalVariable="relocationResult" TargetVariable="result"/>
  </Output>
</Action>
```

`Name` e `Class` possono anche prendere il valore di una variabile del global state, scritta come `this.GLOBALSTATE_<nome>`: l'automa e il file si leggono da quella variabile quando la action viene eseguita.

---

## Note

- Sia `<Input>` che `<Output>` sono opzionali. Una action che non ha effetti collaterali sul global state dell'automa può ometterli.
- Il designer avvisa se il file `.java` non viene trovato nel repository corrente, ma l'automa può comunque funzionare se la classe esiste a runtime.

!!! info
    Per informazioni su quali classi di sistema sono disponibili nel proprio deployment, contattare il team di delivery XAUTOMATA.
