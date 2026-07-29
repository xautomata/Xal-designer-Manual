# Riferimento Actions

Le Actions sono definite all'interno dell'elemento `<Functions>` e rappresentano le operazioni che l'automa esegue quando entra in uno stato associato a quella action.

---

## Elemento Action

```xml
<Action Id="createOrderInERP" Type="object" MinWait="0">
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
| `Type` | Sì | Contesto di esecuzione: `object` per le action legate a oggetti infrastrutturali monitorati, `automaton` per le action che interagiscono con altre istanze di automa. |
| `MinWait` | No | Intervallo minimo in millisecondi tra esecuzioni successive di questa action. |

---

## Elementi figlio

### `<System>`

Specifica l'implementazione runtime della action.

| Attributo | Descrizione |
|---|---|
| `Name` | Il nome del metodo o della funzione da invocare |
| `Class` | Il nome completo della classe che contiene l'implementazione |
| `Path` | Il percorso del file dell'implementazione, relativo alla root del repository |

### `<Query>`

Un'espressione opzionale simile a SQL che fornisce dati di contesto prima dell'esecuzione della action. Utilizzata da alcuni tipi di action per recuperare lo stato dal database della piattaforma.

### `<Input>`

Mappa le variabili del global state dell'automa nei parametri di input della action.

```xml
<Input>
  <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
</Input>
```

### `<Output>`

Mappa i valori di ritorno della action nel global state dell'automa.

```xml
<Output>
  <Parameter LocalVariable="erpOrderId" TargetVariable="erpOrderId"/>
</Output>
```

### `<Async>`

Contrassegna la action come asincrona e imposta un timeout:

```xml
<Async TimeoutSec="30"/>
```

---

## Note

- Sia `<Input>` che `<Output>` sono opzionali. Una action che non ha effetti collaterali sul global state dell'automa può ometterli.
- Il `Class` e il `Path` in `<System>` si riferiscono a implementazioni disponibili nell'ambiente runtime della piattaforma. Il designer avvisa se il file non viene trovato nel repository corrente, ma l'automa può comunque funzionare se la classe esiste a runtime.

!!! info
    Per informazioni su quali classi di sistema sono disponibili nel proprio deployment, contattare il team di delivery XAUTOMATA. Vedere anche `qa.md` — Q15.
