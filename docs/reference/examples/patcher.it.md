# Esempio Annotato — IT Patcher

Questo esempio illustra un **flusso di patching orchestrato** con più modalità operative, attesa basata su clock e un automa osservatore parallelo per le notifiche.

Il file `patcher.xal` modella il patching automatizzato di un cluster di database Oracle, inclusi la rilocazione del database, lo spegnimento dell'istanza, l'aggiornamento del sistema operativo, il riavvio e il riavvio dei servizi.

I frammenti qui sotto vengono da quel file, ridotti agli attributi che contano per ogni punto.

---

## Automi in questo file

| Automa | Ruolo |
|---|---|
| `Patcher` | Orchestratore principale — gestisce l'intera sequenza di patching |
| `RelocateDb` | Sposta le istanze di database fuori dal nodo prima del patching |
| `RelocateBackDb` | Riporta le istanze di database dopo il patching |
| `AppPoolsManager` | Avvia `RecycleAppPools` per gli application pool, poi ripete o termina |
| `RecycleAppPools` | Ricicla un singolo application pool |
| `Dispatcher` | Osservatore — monitora lo stato del Patcher e invia notifiche |

---

## Patcher

L'automa `Patcher` inizia selezionando una delle tre modalità operative:

```xml
<State Id="selectScenario" IdMetric="getScenario"/>
```

```xml
<Transition IdInputState="selectScenario" IdOutputState="balanced"
  IgnoreMinWait="true" MetricValue="balanced"/>
<Transition IdInputState="selectScenario" IdOutputState="human"
  IgnoreMinWait="true" MetricValue="human"/>
<Transition IdInputState="selectScenario" IdOutputState="maintenanceInterval"
  IgnoreMinWait="true" MetricValue="maintenanceInterval"/>
```

Ogni `MetricValue` è uno dei valori elencati nella `Enumeration` della metrica `getScenario`.

- **balanced**: procede automaticamente, rilocando prima i database con `RelocateDb`
- **human**: attende l'esplicita approvazione umana prima di procedere
- **maintenanceInterval**: attende l'apertura di una finestra di manutenzione programmata

Questo pattern di diramazione consente alla stessa logica di patching di essere eseguita in diversi contesti operativi senza duplicare il flusso di lavoro.

### Attesa basata su clock

Dopo il riavvio del sistema operativo, il Patcher attende 20 minuti prima di verificare se Oracle CRS è ripartito:

```xml
<Transition IdInputState="reboot" IdOutputState="checkOracleCRSRunning"
  MetricValue="success">
  <ClockConstraint ClockExp="waitClock &gt;= 20 min"/>
</Transition>
```

La transizione che entra in `reboot` (`executeYumUpdate → reboot`) azzera il clock, quindi i 20 minuti si contano dall'inizio del riavvio. Un solo clock (`waitClock`) serve tutto l'automa: ogni transizione che fa partire una nuova attesa lo azzera.

### Gestione delle eccezioni

Lo stato `exception` monitora la liveness dell'automa Dispatcher:

```xml
<State Id="exception" IdMetric="mEXCEPTION"/>
```

```xml
<Transition IdInputState="exception" IdOutputState="exception"
  IgnoreMinWait="true" MetricValue="dispatchingAlive">
  <ClockConstraint ClockExp="waitClock &gt;= 5 min"/>
</Transition>
<Transition IdInputState="exception" IdOutputState="finished"
  IgnoreMinWait="true" MetricValue="dispatchingDied"/>
```

Se il Dispatcher è ancora in vita, il Patcher attende 5 minuti e riprova. Se il Dispatcher si è fermato, il Patcher termina in modo pulito. Questo garantisce che il sistema di notifica abbia la possibilità di riportare il fallimento prima che il processo termini.

---

## RelocateDb

In modalità `balanced` il Patcher avvia `RelocateDb` e gli passa l'host da liberare:

```xml
<TransitionNew IdInputState="balanced" IdOutputState="checkRelocation"
  IgnoreMinWait="true" Path="patcher.xal" Type="RelocateDb" MinWait="300">
  <ClockReset ClockVar="waitClock"/>
  <Input>
    <Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
  </Input>
</TransitionNew>
```

`Type` è l'`Id` dell'automa figlio, con le stesse maiuscole e minuscole.

La TransitionNew non aspetta il figlio: il Patcher passa subito a `checkRelocation` e da lì legge lo stato di `RelocateDb` con una metrica, finché la rilocazione non è finita. Vedi [Come il padre monitora un figlio](../../concepts/families.md#come-il-padre-monitora-un-figlio).

`RelocateDb` sposta le istanze di database fuori dal nodo una alla volta (`selectNextDB → findNode → relocate`), poi toglie il nodo dal bilanciatore e avvia `AppPoolsManager` con la lista degli application pool:

```xml
<TransitionNew IdInputState="removeFromBalancer" IdOutputState="RecycleAppPools"
  IgnoreMinWait="true" Path="patcher.xal" Type="AppPoolsManager" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPools"/>
  </Input>
</TransitionNew>
```

---

## AppPoolsManager e RecycleAppPools

`AppPoolsManager` avvia un `RecycleAppPools` in `Start` e passa a `Cycle`, dove ricomincia (`next`, di nuovo a `Start`), si ferma (`end`) o fallisce (`error`):

```xml
<TransitionNew IdInputState="Start" IdOutputState="Cycle"
  IgnoreMinWait="true" Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </Input>
</TransitionNew>
```

Ogni istanza di `RecycleAppPools` classifica il pool (bilanciato, ICRIF, batch) e applica la strategia di riavvio adatta.

!!! tip
    Quando la lista è nota in partenza, una [TransitionNewMulti](../transitions.md#transitionnewmulti) avvia un figlio per ogni elemento in un solo passo, senza il ciclo.

---

## Dispatcher

L'automa `Dispatcher` è un **osservatore parallelo**. Il Patcher lo avvia come primissimo passo:

```xml
<TransitionNew IdInputState="initPatch" IdOutputState="selectScenario"
  IgnoreMinWait="true" Path="patcher.xal" Type="Dispatcher" MinWait="600"/>
```

Interroga lo stato del Patcher e invia una notifica ogni volta che il Patcher cambia fase:

```xml
<State Id="polling" IdMetric="getAutomataStatus"/>
<State Id="notifyInit"     IdAction="notify" IdMetric="getNotificationStatus"/>
<State Id="notifyPatching" IdAction="notify" IdMetric="getNotificationStatus"/>
```

Quando il Patcher termina o entra in un'eccezione, il Dispatcher invia una notifica finale e termina. Il Patcher monitora se il Dispatcher è ancora in vita tramite la sua logica di gestione delle eccezioni.

### Il min wait come ritmo di controllo

`MinWait="600"` dà al Dispatcher un [min wait](../../designer/transitions/types.md#min-wait-e-ignore-min-wait) di 600 secondi: dopo ogni transizione resta almeno 10 minuti nel nuovo stato, a meno che la transizione in uscita abbia `IgnoreMinWait`. Il ciclo su `polling` non ce l'ha, quindi il Dispatcher controlla il Patcher ogni 10 minuti; le transizioni verso gli stati di notifica ce l'hanno, quindi la notifica parte subito:

```xml
<Transition IdInputState="polling" IdOutputState="polling" MetricValue="waiting"/>
<Transition IdInputState="polling" IdOutputState="notifyPatching"
  IgnoreMinWait="true" MetricValue="patching"/>
```

---

## Pattern chiave illustrati

- **Orchestrazione multi-modalità**: diramazione all'avvio per selezionare la modalità operativa
- **Ritardi basati su clock**: uso di `ClockConstraint` per imporre tempi di attesa tra i passi
- **Osservatore parallelo**: un automa figlio dedicato che monitora e riporta sullo stato del padre
- **Monitoraggio reciproco**: padre e figlio che monitorano reciprocamente la liveness
- **Ciclo sui figli**: `AppPoolsManager` che avvia un figlio e decide se ricominciare
- **Min wait come ritmo di controllo**: il min wait di un figlio stabilisce ogni quanto controlla, con `IgnoreMinWait` sulle transizioni che non devono aspettare
- **Passaggio di parametri**: il nome dell'host e la lista degli application pool passati lungo la gerarchia degli automi
