# Actions e Metrics

Actions e metrics sono i due tipi di funzione che connettono un automaton al mondo esterno. Sono definite a livello di automaton e associate ai singoli stati.

---

## Metrics

Una metric è una funzione che **legge dati** da un sistema monitorato e restituisce un valore.

Quando un automaton entra in uno stato associato a una metric, esegue la metric una volta. Il valore restituito dalla metric viene quindi confrontato con i valori di metric definiti sulle transizioni uscenti da quello stato — e la transizione corrispondente si attiva. Per leggere di nuovo la metric, l'automaton deve rientrare nello stato, di solito con un self-loop.

Molte metrics leggono lo stato operativo di un altro automaton. Lo fanno tramite una query, scritta in una sintassi simile a SQL, che recupera il global state corrente del target.

Una metric definisce anche l'insieme dei valori che può restituire, chiamato **enumerazione**. Ogni valore nell'enumerazione corrisponde a un possibile esito — e tipicamente a una Transition che gestisce quell'esito.

---

## Actions

Un'action è una funzione che **esegue un'operazione** su un sistema esterno.

Quando un automaton entra in uno stato associato a un'action, esegue quell'action. Le actions possono interagire con sistemi esterni come piattaforme ERP, sistemi di gestione magazzino, strumenti di ticketing o API personalizzate.

Un'action non sceglie la prossima Transition: lo fa solo il valore della metric. Un'action può scrivere i suoi risultati nel global state tramite i parametri output. Uno stato con un'action e senza metric esce con una Transition senza Metric Value.

---

## Come metrics e actions lavorano insieme

Uno stato può avere sia un'action che una metric. Prima viene eseguita l'action, poi la metric legge il risultato.

La metric viene eseguita una sola volta per ogni ingresso nello stato, quindi un processo che deve aspettare un sistema esterno di solito divide il lavoro in due stati: uno che esegue l'action, e uno che controlla il risultato con una metric e un self-loop finché non viene rilevata una condizione di completamento o di errore.

Ad esempio, in un automaton per l'elaborazione ordini:

- Uno stato con action invia una lista di picking a un sistema di gestione magazzino.
- L'automaton si sposta poi in uno stato di attesa dove una metric verifica se il WMS ha confermato il picking, e un self-loop ripete il controllo finché non lo ha fatto.
- Quando la metric restituisce `confirmed`, la Transition si attiva e il processo continua.

---

## Il meccanismo di query

Le metrics usano una query simile a SQL per leggere lo stato di un altro automaton. La query punta a una tabella virtuale che rappresenta il global state di una specifica istanza di automaton, identificata da un group label.

Il group label è un meccanismo di binding che collega la query all'istanza corretta dell'automaton target — quella in esecuzione sullo stesso oggetto monitorato dell'automaton che interroga.

!!! info
    I dettagli tecnici della sintassi di query e del binding del group label sono trattati nella sezione [XAL Reference — Query Syntax](../reference/queries.md).

---

## Parametri input e output

Sia le metrics che le actions scambiano dati con l'automaton tramite parametri **input** e **output**. I parametri input forniscono valori alla funzione dal global state dell'automaton. I parametri output scrivono i risultati nel global state al termine dell'esecuzione della funzione.

Per passare dati a un automaton figlio quando parte, per esempio un ID ordine, usa invece i parametri input della [TransitionNew](../designer/transitions/transition_new.md#parametri-di-input).

---

## Funzioni che eseguono un automaton

Di solito un'action o una metric esegue un metodo Java. Può anche eseguire **un altro automaton**: l'automaton chiamante aspetta nello stato finché quello chiamato non finisce, poi ne riceve i valori di output. Vedi [Famiglie di automata](families.md#eseguire-un-automaton-come-passo).

---

## Quando una funzione fallisce

Se il metodo segnala un errore temporaneo, la piattaforma lo riesegue ogni minuto, fino a dieci volte. Dopo, o dopo un errore fatale, l'istanza dell'automaton si ferma e aspetta un intervento manuale.
