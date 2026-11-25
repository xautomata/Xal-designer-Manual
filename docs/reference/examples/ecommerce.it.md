# Esempio Annotato — E-commerce Backoffice

Questo esempio illustra il pattern **Starter / Operator**: un automa persistente che monitora un sistema esterno alla ricerca di eventi, e crea un automa figlio dedicato per gestirne ciascuno.

Il file `ecommerce_backoffice.xal` modella l'elaborazione backend degli ordini e-commerce, integrando un negozio Shopify con un ERP e un sistema di gestione magazzino (WMS).

I frammenti qui sotto vengono da quel file, ridotti agli attributi che contano per ogni punto.

---

## Automi in questo file

| Automa | Ruolo |
|---|---|
| `Starter` | Interroga Shopify per i nuovi ordini e crea un `Operator` per ogni ordine |
| `Operator` | Gestisce l'intero ciclo di vita di un singolo ordine |
| `SyncInvoiceAndDDTHeader_PickingList` | Sotto-processo: sincronizza i dati di fatturazione e invia una lista di prelievo al WMS |

---

## Starter

L'automa `Starter` è in esecuzione continua. Non ha uno stato finale diverso da `exception`, il che significa che è progettato per funzionare indefinitamente.

```xml
<Automaton Id="Starter">
  <GlobalState>
    <Variable Name="shopifyApiEndpoint" Type="string"/>
    <Variable Name="shopifyCredentials" Type="string"/>
    <Variable Name="lastProcessedOrders" Type="list[string]" Value="[]"/>
    <Variable Name="communicationErrorCount" Type="int" Value="0"/>
  </GlobalState>
```

Il global state memorizza le credenziali API e una lista degli ID degli ordini già elaborati, che la metric usa per evitare di elaborare lo stesso ordine due volte.

```xml
  <Transitions>
    <Transition IdInputState="init" IdOutputState="newOrder" IgnoreMinWait="true"/>

    <Transition IdInputState="newOrder" IdOutputState="newOrder"
      MetricValue="noNewOrders" IgnoreMinWait="true"/>

    <Transition IdInputState="newOrder" IdOutputState="instantiatedOperator"
      MetricValue="newOrderFound" IgnoreMinWait="true"/>
    <TransitionNew IdInputState="newOrder" IdOutputState="instantiatedOperator"
      IgnoreMinWait="true" Path="ecommerce_backoffice.xal" Type="Operator">
      <Input>
        <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
        <Parameter LocalVariable="shopifyCredentials" TargetVariable="shopifyCredentials"/>
      </Input>
    </TransitionNew>
```

Le transizioni chiave sono:

- `newOrder → newOrder` (MetricValue `noNewOrders`): l'automa torna su se stesso finché non vengono rilevati nuovi ordini
- `newOrder → instantiatedOperator` (Transition + TransitionNew): quando viene trovato un nuovo ordine, viene creato un `Operator` che riceve l'endpoint e le credenziali Shopify
- `instantiatedOperator → newOrder`: immediatamente dopo la creazione, lo Starter torna a monitorare il prossimo ordine

Una TransitionNew non ha `MetricValue`, quindi da sola sarebbe valida qualunque cosa dica la metrica, insieme al ciclo `noNewOrders`. La Transition tra gli stessi due stati le dà la condizione `newOrderFound`: l'interprete fonde le due in una sola transizione. Vedi [TransitionNew](../transitions.md#transitionnew).

Questo crea un event loop: un Operator per ordine, eseguiti in parallelo, mentre lo Starter continua a monitorare.

### Riprova con un limite di tempo

Quando Shopify non risponde, lo Starter passa a `communicationError` e azzera due clock. Riprova ogni 5 minuti; quando la metrica riporta `timeout` si arrende, ma non prima di 30 minuti:

```xml
<Transition IdInputState="communicationError" IdOutputState="communicationError"
  MetricValue="connectionError">
  <ClockConstraint ClockExp="retryTimer &gt;= 5 min"/>
  <ClockReset ClockVar="retryTimer"/>
</Transition>
<Transition IdInputState="communicationError" IdOutputState="newOrder"
  IgnoreMinWait="true" MetricValue="connected"/>
<Transition IdInputState="communicationError" IdOutputState="exception"
  MetricValue="timeout">
  <ClockConstraint ClockExp="communicationErrorTimer &gt;= 30 min"/>
</Transition>
```

`retryTimer` si azzera a ogni tentativo, quindi misura il tempo dall'ultimo; `communicationErrorTimer` si azzera solo all'ingresso nello stato, quindi misura tutta l'interruzione.

---

## Operator

L'automa `Operator` gestisce un singolo ordine dal ricevimento alla fattura. Ogni stato corrisponde a un passo nel flusso di fulfillment.

```xml
<States>
  <State Id="getEcommOrder"     IdAction="getEcommOrderAction"/>
  <State Id="putErpOrder"       IdAction="putErpOrderAction"/>
  <State Id="waitingWMSforPicking" IdMetric="checkWMSPickingConfirmation"/>
  <State Id="putErpDeliveryNote"   IdAction="putErpDeliveryNoteAction"/>
  <State Id="askForShippingApproval" IdMetric="checkShippingApproval"/>
  <State Id="runWMSShipping"    IdAction="runWMSShippingAction"/>
  <State Id="waitingWMSforShipped" IdMetric="checkWMSShipped"/>
  <State Id="runErpDDT"         IdAction="runErpDDTAction"/>
  <State Id="runErpInvoice"     IdAction="runErpInvoiceAction"/>
  <State Id="completed"/>
  <State Id="exception"         IdMetric="handleException"/>
  <State Id="manualIntervention"/>
</States>
```

Il flusso è lineare:

1. Recupera l'ordine da Shopify
2. Crea l'ordine nell'ERP → crea `SyncInvoiceAndDDTHeader_PickingList` per sincronizzare le intestazioni e inviare la lista di prelievo al WMS
3. Attende la conferma di picking dal WMS
4. Crea la pre-nota di consegna nell'ERP
5. Attende l'approvazione di spedizione dall'operatore
6. Autorizza il WMS alla spedizione
7. Attende la conferma di spedizione dal WMS
8. Genera il documento DDT
9. Genera la fattura → `completed`

Gli stati di attesa passano a `exception` quando la loro metric riporta un problema (`error`, o `rejected` per l'approvazione della spedizione). Lì la metric `handleException` decide se ripartire da `getEcommOrder` dopo 5 minuti o escalare verso `manualIntervention`.

---

## SyncInvoiceAndDDTHeader_PickingList

Questo automa figlio gestisce il sotto-processo di sincronizzazione attivato dopo la creazione dell'ordine nell'ERP. Esegue quattro action in sequenza:

```
syncInvoiceHeader → syncDDTHeader → pickingListFromERP → pickingListToWMS → completed
```

Viene creato dall'Operator tramite TransitionNew e riceve come parametri di input le credenziali dell'ERP e del WMS e i dati dell'ordine. L'Operator non lo aspetta: passa a `waitingWMSforPicking`, dove attende l'effetto del figlio sul WMS (la conferma di picking) e non il figlio stesso.

---

## Pattern chiave illustrati

- **Starter / Operator**: un osservatore di lunga durata che crea gestori di breve durata
- **Pipeline di action lineare**: stati associati solo ad action, nessuna osservazione di metric necessaria tra i passi
- **Stati di attesa**: stati associati a metric che interrogano un sistema esterno finché una condizione non viene soddisfatta (`pending` torna in loop, `confirmed` avanza)
- **Riprova con un limite di tempo**: due clock, uno per il ritmo dei tentativi e uno per il tempo totale concesso
- **Gestione delle eccezioni con retry**: uno stato `exception` dedicato con una metric che decide se ripartire dall'inizio o richiedere intervento manuale
- **Passaggio di parametri alla creazione**: credenziali e contesto passati dallo Starter all'Operator, e dall'Operator al sotto-processo
