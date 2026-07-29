# Actions e Metrics

Actions e metrics sono i due tipi di funzione che connettono un automaton al mondo esterno. Sono definite a livello di automaton e associate ai singoli stati.

---

## Metrics

Una metric è una funzione che **legge dati** da un sistema monitorato e restituisce un valore.

Quando un automaton entra in uno stato associato a una metric, inizia a osservare quella metric. Il valore restituito dalla metric viene quindi confrontato con i valori di metric definiti sulle transizioni uscenti da quello stato — e la transizione corrispondente si attiva.

Le metrics leggono tipicamente lo stato operativo di un altro automaton o di un oggetto infrastrutturale monitorato. Lo fanno tramite una query, scritta in una sintassi simile a SQL, che recupera il global state corrente del target.

Una metric definisce anche l'insieme dei valori che può restituire, chiamato **enumerazione**. Ogni valore nell'enumerazione corrisponde a un possibile esito — e tipicamente a una Transition che gestisce quell'esito.

---

## Actions

Un'action è una funzione che **esegue un'operazione** su un sistema esterno.

Quando un automaton entra in uno stato associato a un'action, esegue quell'action. Le actions possono interagire con sistemi esterni come piattaforme ERP, sistemi di gestione magazzino, strumenti di ticketing o API personalizzate.

A differenza delle metrics, le actions non restituiscono un valore che guida direttamente le transizioni. La Transition in uscita da uno stato con action è determinata dall'esito dell'action, gestito separatamente.

---

## Come metrics e actions lavorano insieme

In pratica, uno stato ha spesso sia una metric che un'action associate. L'action svolge il lavoro, e la metric monitora il risultato finché non viene rilevata una condizione di completamento o di errore.

Ad esempio, in un automaton per l'elaborazione ordini:

- Uno stato con action invia una lista di picking a un sistema di gestione magazzino.
- L'automaton si sposta poi in uno stato di attesa dove una metric verifica continuamente se il WMS ha confermato il picking.
- Quando la metric restituisce `confirmed`, la Transition si attiva e il processo continua.

---

## Il meccanismo di query

Le metrics usano una query simile a SQL per leggere lo stato di un altro automaton. La query punta a una tabella virtuale che rappresenta il global state di una specifica istanza di automaton, identificata da un group label.

Il group label è un meccanismo di binding che collega la query all'istanza corretta dell'automaton target — quella in esecuzione sullo stesso oggetto monitorato dell'automaton che interroga.

!!! info
    I dettagli tecnici della sintassi di query e del binding del group label sono trattati nella sezione [XAL Reference — Query Syntax](../reference/queries.md).

---

## Tipi di dato

Sia le metrics che le actions possono trasferire dati tra automata usando parametri **input** e **output**. I parametri input forniscono valori alla funzione dal global state dell'automaton. I parametri output scrivono i risultati nel global state al termine dell'esecuzione della funzione.

Questo meccanismo permette agli automata di condividere contesto — ad esempio, passando un ID ordine da un automaton padre a un automaton figlio al momento della sua creazione.
