# Riferimento Transitions

Le Transitions definiscono come l'automa si muove tra gli stati. Sono disponibili quattro tipi di transizione, ciascuno con il proprio nome di elemento e insieme di attributi.

---

## Attributi comuni

Tutti i tipi di transizione condividono questi attributi:

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `IdInputState` | Sì | ID dello stato in cui l'automa deve trovarsi affinché la transizione scatti |
| `IdOutputState` | Sì | ID dello stato verso cui l'automa si sposta quando la transizione scatta |
| `IgnoreMinWait` | No | Se `true`, bypassa l'intervallo minimo di attesa tra scatti successivi |

---

## Transition

La transizione standard. Scatta quando un valore della metric corrisponde e/o le condizioni degli orologi sono soddisfatte.

```xml
<Transition
  IdInputState="Waiting"
  IdOutputState="Processing"
  MetricValue="confirmed"
  IgnoreMinWait="true">
  <ClockConstraint ClockExp="clk_wait &lt; T_max_wait"/>
  <ClockReset ClockVar="clk_wait"/>
</Transition>
```

Attributi aggiuntivi:

| Attributo | Descrizione |
|---|---|
| `MetricValue` | Il valore di ritorno della metric che attiva la transizione |

Elementi figlio:

| Elemento | Descrizione |
|---|---|
| `<ClockConstraint ClockExp="..."/>` | Una condizione basata sul tempo. Sono ammessi più elementi. |
| `<ClockReset ClockVar="..."/>` | Resetta il clock specificato quando la transizione scatta. Sono ammessi più elementi. |

---

## TransitionNew

Crea un automa figlio e si sposta verso lo stato di output. Utilizzata per costruire famiglie di automi.

```xml
<TransitionNew
  IdInputState="Init"
  IdOutputState="Processing"
  Path="ecommerce_backoffice.xal"
  Type="Operator"
  MinWait="300"
  IgnoreMinWait="true">
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</TransitionNew>
```

Attributi aggiuntivi:

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Path` | Sì | Nome del file XAL contenente l'automa di destinazione |
| `Type` | Sì | ID dell'automa da istanziare |
| `MinWait` | No | Ritardo minimo in secondi prima che il figlio inizi l'elaborazione |

Elementi figlio:

| Elemento | Descrizione |
|---|---|
| `<Input><Parameter .../></Input>` | Mappa le variabili del global state del padre sulle variabili del global state del figlio |
| `<ClockConstraint>`, `<ClockReset>` | Come in `Transition` |

---

## TransitionNewMulti

Come `TransitionNew`, ma itera su una variabile lista e crea un'istanza figlio per ogni elemento.

```xml
<TransitionNewMulti
  IdInputState="Init"
  IdOutputState="Running"
  Path="patcher.xal"
  Type="RecycleAppPools"
  MinWait="300">
  <LoopInput>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </LoopInput>
  <Input>
    <Parameter LocalVariable="erpOrderId" TargetVariable="orderId"/>
  </Input>
</TransitionNewMulti>
```

Elementi figlio aggiuntivi:

| Elemento | Descrizione |
|---|---|
| `<LoopInput><Parameter .../></LoopInput>` | Una singola variabile lista dal global state del padre. Viene creato un figlio per ogni elemento. |

---

## TransitionX

Una transizione estesa che combina le caratteristiche di `Transition` e `TransitionNew`. Supporta sia una condizione `MetricValue` sia la possibilità di creare un automa figlio tramite un elemento figlio `<New>`.

```xml
<TransitionX
  IdInputState="Active"
  IdOutputState="Handling"
  MetricValue="alert">
  <New Path="handlers.xal" Type="AlertHandler" MinWait="300"/>
  <ClockReset ClockVar="clk_alert"/>
</TransitionX>
```

Elementi figlio aggiuntivi:

| Elemento | Descrizione |
|---|---|
| `<New Path="..." Type="..." MinWait="...">` | Crea un automa figlio. Supporta `<Input>` e `<LoopInput>` come figli. |

---

## Uso di `<` e `>` nelle espressioni di clock

XML richiede che le parentesi angolari siano escapate all'interno dei valori degli attributi. Usare `&lt;` per `<` e `&gt;` per `>` nelle espressioni `ClockExp`:

```xml
<ClockConstraint ClockExp="clk_retry &lt; T_max_wait"/>
<ClockConstraint ClockExp="clk_auth &gt;= T_auth_timeout"/>
```
