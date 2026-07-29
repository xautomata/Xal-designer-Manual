# Tipi di Correlazione

Il Process Overview mostra tre tipi di relazioni tra automi. Ogni tipo è rappresentato da uno stile di linea distinto e può essere attivato o disattivato in modo indipendente.

---

## SQL-like

Una correlazione **SQL-like** (linea blu continua) collega un automa a un altro il cui stato legge tramite una query Metric.

Quando una Metric nell'automa A contiene una query che legge lo stato globale dell'automa B, il designer disegna un arco SQL-like da A verso B. Questo rappresenta una dipendenza dai dati: A osserva lo stato di B per guidare le proprie transizioni.

Questo è il tipo di correlazione più comune negli scenari di monitoraggio.

---

## New

Una correlazione **New** (linea viola tratteggiata) collega un automa genitore a un automa figlio che esso istanzia.

Quando un automa utilizza una `TransitionNew` o `TransitionNewMulti` per istanziare un altro automa, il designer disegna un arco New dal genitore al figlio. Questo rappresenta una dipendenza del ciclo di vita: il genitore crea e monitora il figlio come parte del suo flusso di processo.

---

## Family Tree

Una correlazione **Family Tree** (linea arancione punteggiata) collega un automa a un sistema esterno o a una funzione che chiama direttamente tramite la sua configurazione `System`, usando il tipo di funzione `automaton`.

Questo tipo di correlazione è meno comune e rappresenta una dipendenza di integrazione a basso livello.

---

## Nodi orfani

Un **orfano** è un automa che non ha connessioni con altri automi nella vista corrente. Gli orfani possono essere automi autonomi, automi le cui connessioni sono filtrate, oppure automi che si connettono solo a repository esterni.

Usa il toggle **Show Orphans** nel pannello filtri in alto a destra per includere o escludere i nodi orfani dal grafico.

![Pannello filtri tipi di correlazione](../../images/designer/overview_graph/filters.png)
/// caption
Fig.1 - Il pannello filtri dei tipi di correlazione
///
