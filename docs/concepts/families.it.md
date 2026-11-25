# Famiglie di automata

I processi complessi raramente vengono modellati con un singolo automaton. Vengono invece composti da più automata che lavorano insieme — una **famiglia**.

---

## Automata padre e figlio

In una famiglia di automata, un automaton agisce da **orchestratore**. Gestisce il flusso complessivo del processo e delega task specifici ad **automata figlio** che crea a runtime.

Ogni automaton figlio ha i propri stati, le proprie transizioni e il proprio ciclo di vita. Un padre può avviare un figlio in due modi:

- con una **TransitionNew**: il figlio gira per conto suo, e il padre lo segue leggendone lo stato;
- con un'**action o una metric di tipo Automaton**: il figlio gira come un passo del padre, che aspetta che finisca.

Questa separazione mantiene ogni automaton focalizzato su una singola responsabilità, rendendo il sistema complessivo più facile da comprendere, mantenere e riutilizzare.

---

## Creare automata figlio

Un automaton padre crea un figlio usando una transizione **TransitionNew**. Quando questa Transition si attiva, viene creata una nuova istanza dell'automaton specificato e inizia a girare immediatamente. Il padre passa allo stato di output della Transition e prosegue con il proprio lavoro.

Il padre può passare dati al figlio al momento della creazione, tramite parametri input. Ad esempio, un padre che gestisce un processo ordini potrebbe passare l'ID ordine e le credenziali API all'automaton figlio responsabile della creazione dell'ordine in un sistema ERP.

Una **TransitionNewMulti** avvia un figlio per ogni elemento di una lista, per esempio uno per ogni database da rilocare.

Una volta avviato, il figlio non è legato al padre: non gli restituisce nulla, e continua a girare anche se il padre finisce prima. Condivide il **group label** del padre, che permette al padre di trovarlo. Vedi [Istanziare automi figli](../designer/transitions/transition_new.md) per i campi.

---

## Come il padre monitora un figlio

Una volta che un figlio è in esecuzione, il padre ne monitora il progresso tramite una **metric**. La metric legge il global state del figlio — nello specifico, il suo stato corrente — usando una query simile a SQL sulla tabella del figlio, filtrata per il group label che condividono.

Il padre effettua le transizioni in base al valore restituito da questa metric. Se il figlio raggiunge uno stato di successo, il padre avanza al passo successivo. Se il figlio raggiunge uno stato di errore, il padre può riprovare, escalare o interrompere.

---

## Eseguire un automaton come passo

Un'action o una metric può eseguire un altro automaton al posto di un metodo Java (vedi [Type Automaton](../designer/states/actions.md#type-automaton)). In questo caso:

1. il figlio parte con i valori impostati in **Action Sign** (o **Metric Sign**);
2. il padre resta nel suo stato e non fa altro finché il figlio non raggiunge uno stato finale;
3. i valori di output del figlio vengono copiati nei Global State del padre, come impostato in **Action Return** (o **Metric Return**). Per una metric, il valore contrassegnato come simbolo diventa il valore della metric, e sceglie la prossima Transition del padre.

Usalo quando il padre non può proseguire senza il risultato del figlio. Nel Process Overview questo legame è disegnato come una correlazione [Family Tree](../designer/overview_graph/correlation_types.md#family-tree).

---

## Pattern comuni

**Starter / Operator** è un pattern usato quando un processo deve girare una volta per ogni evento. Un automaton persistente (lo Starter) monitora continuamente un sistema esterno alla ricerca di nuovi eventi. Quando ne rileva uno, crea un automaton Operator per gestire quell'evento specifico. Lo Starter torna poi a monitorare il prossimo evento, mentre l'Operator gestisce autonomamente l'intero ciclo di vita del processo.

Questo pattern è comune negli scenari di integrazione — ad esempio, creando un Operator per ogni ordine in ingresso da una piattaforma e-commerce.

**Coordinator / Worker** è un pattern in cui un automaton orchestratore gestisce un insieme di automata worker, ciascuno responsabile di un singolo elemento. Il coordinator crea i worker, di solito con una TransitionNewMulti, ne monitora il progresso e ne aggrega i risultati. Questo pattern viene usato quando la stessa operazione deve essere eseguita su più elementi in parallelo.

---

## Automata tra repository diversi

La piattaforma non vede i repository: vede i file XAL che carica. Ogni automaton di un file caricato ha la propria tabella, e una query può leggere la tabella di uno qualsiasi di essi, da qualunque file o repository provenga (vedi [Sintassi delle Query](../reference/queries.md#convenzione-di-denominazione-delle-tabelle)).

Quindi automata in repository diversi possono comunicare, purché la piattaforma carichi entrambi i file. Se il file dell'automaton target non è caricato, non c'è nessuna tabella da leggere. Lo stesso vale per l'automaton avviato da una TransitionNew: anche il suo file deve essere caricato.

Nel Process Overview dello XAL Designer, un automaton citato ma non definito nel repository corrente è disegnato come una nuvola (vedi [Leggere il Process Overview](../designer/overview_graph/reading.md#forme-dei-nodi)).
