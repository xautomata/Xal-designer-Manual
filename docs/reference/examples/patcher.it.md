# Esempio Annotato — IT Patcher

Questo esempio illustra un **flusso di patching orchestrato** con più modalità operative, attesa basata su clock e un automa osservatore parallelo per le notifiche.

Il file `patcher.xal` modella il patching automatizzato di un cluster di database Oracle, inclusi la rilocazione del database, lo spegnimento dell'istanza, l'aggiornamento del sistema operativo, il riavvio e il riavvio dei servizi.

---

## Automi in questo file

| Automa | Ruolo |
|---|---|
| `Patcher` | Orchestratore principale — gestisce l'intera sequenza di patching |
| `RelocateDb` | Sposta le istanze di database fuori dal nodo prima del patching |
| `RelocateBackDb` | Riporta le istanze di database dopo il patching |
| `AppPoolsManager` | Itera su una lista di application pool e ricicla ciascuno |
| `RecycleAppPools` | Ricicla un singolo application pool |
| `Dispatcher` | Osservatore — monitora lo stato del Patcher e invia notifiche |

---

## Patcher

L'automa `Patcher` inizia selezionando una delle tre modalità operative:

```xml
<State Id="selectScenario" IdMetric="getScenario"/>
```

```xml
<Transition IdInputState="selectScenario" IdOutputState="balanced"    MetricValue="balanced"/>
<Transition IdInputState="selectScenario" IdOutputState="human"        MetricValue="human"/>
<Transition IdInputState="selectScenario" IdOutputState="maintenanceInterval" MetricValue="maintenance"/>
```

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

Un singolo clock (`waitClock`) viene riutilizzato nell'intero automa, resettato ad ogni passo che richiede un timer fresco.

### Gestione delle eccezioni

Lo stato `exception` monitora la liveness dell'automa Dispatcher:

```xml
<State Id="exception" IdMetric="mEXCEPTION"/>
```

```xml
<Transition IdInputState="exception" IdOutputState="exception"
  MetricValue="dispatchingAlive">
  <ClockConstraint ClockExp="waitClock &gt;= 5 min"/>
</Transition>
<Transition IdInputState="exception" IdOutputState="finished"
  MetricValue="dispatchingDied"/>
```

Se il Dispatcher è ancora in vita, il Patcher attende 5 minuti e riprova. Se il Dispatcher si è fermato, il Patcher termina in modo pulito. Questo garantisce che il sistema di notifica abbia la possibilità di riportare il fallimento prima che il processo termini.

---

## RelocateDb

`RelocateDb` itera sulle istanze del database, rilocando ciascuna su un nodo diverso prima dell'inizio del patching. Poi crea `AppPoolsManager` per riciclare gli application pool.

```xml
<TransitionNew IdInputState="removeFromBalancer" IdOutputState="RecycleAppPools"
  Path="patcher.xal" Type="AppPoolsManager" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPools"/>
  </Input>
</TransitionNew>
```

La lista degli application pool viene passata come parametro di input ad `AppPoolsManager`.

---

## AppPoolsManager e RecycleAppPools

`AppPoolsManager` implementa un loop su una lista di application pool usando manualmente il pattern di `TransitionNewMulti` — crea un figlio `RecycleAppPools` per ogni pool, scorrendo la lista:

```xml
<TransitionNew IdInputState="Start" IdOutputState="Cycle"
  Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <Input>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </Input>
</TransitionNew>
```

Ogni istanza di `RecycleAppPools` gestisce un pool, classificandolo e applicando la strategia di riavvio appropriata.

---

## Dispatcher

L'automa `Dispatcher` è un **osservatore parallelo**. Viene creato dal Patcher fin dall'inizio:

```xml
<TransitionNew IdInputState="initPatch" IdOutputState="selectScenario"
  Path="patcher.xal" Type="Dispatcher" MinWait="600"/>
```

Interroga continuamente il global state del Patcher e invia una notifica ogni volta che il Patcher cambia fase:

```xml
<State Id="polling" IdMetric="getAutomataStatus"/>
<State Id="notifyInit"     IdAction="notify" IdMetric="getNotificationStatus"/>
<State Id="notifyPatching" IdAction="notify" IdMetric="getNotificationStatus"/>
```

Quando il Patcher termina o entra in un'eccezione, il Dispatcher invia una notifica finale e termina. Il Patcher monitora se il Dispatcher è ancora in vita tramite la sua logica di gestione delle eccezioni.

---

## Pattern chiave illustrati

- **Orchestrazione multi-modalità**: diramazione all'avvio per selezionare la modalità operativa
- **Ritardi basati su clock**: uso di `ClockConstraint` per imporre tempi di attesa tra i passi
- **Osservatore parallelo**: un automa figlio dedicato che monitora e riporta sullo stato del padre
- **Monitoraggio reciproco**: padre e figlio che monitorano reciprocamente la liveness
- **Iterazione su lista**: `AppPoolsManager` che scorre una lista creando ripetutamente figli e attendendone il completamento
- **Passaggio di parametri**: credenziali e liste passate attraverso la gerarchia degli automi
