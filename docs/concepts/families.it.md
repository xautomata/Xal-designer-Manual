# Famiglie di automata

I processi complessi raramente vengono modellati con un singolo automaton. Vengono invece composti da più automata che lavorano insieme — una **famiglia**.

---

## Automata padre e figlio

In una famiglia di automata, un automaton agisce da **orchestratore**. Gestisce il flusso complessivo del processo e delega task specifici ad **automata figlio** che crea a runtime.

Ogni automaton figlio gira in modo indipendente. Ha i propri stati, le proprie transizioni e il proprio ciclo di vita. Quando raggiunge uno stato finale, l'automaton padre legge il suo esito tramite una metric e decide il passo successivo.

Questa separazione mantiene ogni automaton focalizzato su una singola responsabilità, rendendo il sistema complessivo più facile da comprendere, mantenere e riutilizzare.

---

## Creare automata figlio

Un automaton padre crea un figlio usando una transizione **TransitionNew**. Quando questa Transition si attiva, viene creata una nuova istanza dell'automaton specificato e inizia a girare immediatamente.

Il padre può passare dati al figlio al momento della creazione, tramite parametri input. Ad esempio, un padre che gestisce un processo ordini potrebbe passare l'ID ordine e le credenziali API all'automaton figlio responsabile della creazione dell'ordine in un sistema ERP.

---

## Come il padre monitora un figlio

Una volta che un figlio è in esecuzione, il padre ne monitora il progresso tramite una **metric**. La metric legge il global state del figlio — nello specifico, il suo stato corrente — usando una query simile a SQL.

Il padre effettua le transizioni in base al valore restituito da questa metric. Se il figlio raggiunge uno stato di successo, il padre avanza al passo successivo. Se il figlio raggiunge uno stato di errore, il padre può riprovare, escalare o interrompere.

---

## Pattern comuni

**Starter / Operator** è un pattern usato quando un processo deve girare una volta per ogni evento. Un automaton persistente (lo Starter) monitora continuamente un sistema esterno alla ricerca di nuovi eventi. Quando ne rileva uno, crea un automaton Operator per gestire quell'evento specifico. Lo Starter torna poi a monitorare il prossimo evento, mentre l'Operator gestisce autonomamente l'intero ciclo di vita del processo.

Questo pattern è comune negli scenari di integrazione — ad esempio, creando un Operator per ogni ordine in ingresso da una piattaforma e-commerce.

**Coordinator / Worker** è un pattern in cui un automaton orchestratore gestisce un insieme di automata worker, ciascuno responsabile di un singolo elemento. Il coordinator crea i worker, ne monitora il progresso e ne aggrega i risultati. Questo pattern viene usato quando la stessa operazione deve essere eseguita su più elementi in parallelo.

---

## Automata tra repository diversi

Gli automata in repository diversi possono comunicare a runtime. Un automaton può leggere lo stato di un altro tramite il meccanismo di query delle metrics, anche se sono definiti in file o repository separati.

Nella Process Overview dello XAL Designer, i riferimenti ad automata che esistono al di fuori del repository corrente vengono mostrati come nodi distinti, visivamente differenziati dagli automata definiti nel repository.

!!! info
    Il meccanismo tecnico alla base della comunicazione cross-repository è tracciato come domanda aperta. Vedi `qa.md` — Q18.
