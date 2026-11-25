# Stati e Transitions

Gli stati e le Transitions sono i mattoni fondamentali di ogni automaton. Capire come lavorano insieme è indispensabile prima di usare lo XAL Designer.

---

## Stati

Uno stato rappresenta una condizione o fase nel ciclo di vita del processo monitorato.

Ogni automaton ha esattamente uno **stato iniziale** — lo stato in cui si trova quando viene istanziato per la prima volta. Può avere anche uno o più **stati finali** — stati che segnano la fine del ciclo di vita dell'automaton. Quando viene raggiunto uno stato finale, l'automaton smette di elaborare.

Tutti gli altri stati sono stati intermedi, che rappresentano le fasi attive del processo.

Nello XAL Designer, il colore del bordo del nodo e della sua striscia a sinistra indica il tipo di stato:

| Colore | Significato |
|---|---|
| Verde | Stato iniziale |
| Rosso | Stato finale |
| Blu | Stato intermedio |

Uno stato può avere un'**action**, una **metric**, o entrambe. Quando l'automaton entra nello stato, esegue prima l'action, poi la metric, una volta ciascuna. Per leggere di nuovo la metric, l'automaton deve rientrare nello stato, per esempio con un [self-loop](#self-loop).

---

## Transitions

Una Transition definisce la condizione in base alla quale l'automaton passa da uno stato all'altro.

Ogni Transition ha:

- uno **stato di input** — lo stato in cui deve trovarsi l'automaton
- uno **stato di output** — lo stato in cui si sposta l'automaton
- opzionalmente, un **valore di metric** — il valore specifico che attiva la Transition
- opzionalmente, uno o più **clock constraint** — condizioni temporali che devono essere soddisfatte

Quando l'automaton si trova nello stato di input e tutte le condizioni della Transition sono soddisfatte, la Transition si attiva e l'automaton si sposta nello stato di output.

---

## Come viene scelta la prossima Transition

Dopo aver eseguito l'action e la metric di uno stato, l'automaton guarda le Transitions che ne escono:

- se è valida **una sola** Transition, questa si attiva;
- se il valore della metric corrisponde a una Transition ma i suoi clock constraint non sono ancora soddisfatti, l'automaton aspetta nello stato finché non lo diventano;
- se **nessuna** Transition prevede il valore della metric, o ne sono valide **due o più** nello stesso momento, l'automaton si ferma con un errore.

Progetta ogni stato in modo che ogni valore che la metric può restituire porti a una sola Transition.

---

## Tipi di Transition

Lo XAL Designer crea tre tipi di Transition. Un quarto, TransitionX, si può solo leggere.

**Transition** è il tipo standard. Si attiva quando viene osservato un valore di metric specifico, quando un clock constraint è soddisfatto, o incondizionatamente se non sono definite condizioni.

**TransitionNew** avvia un automaton figlio quando si attiva. Il padre passa al suo stato di output e il figlio gira per conto suo. Così si costruiscono le famiglie di automata.

**TransitionNewMulti** funziona come TransitionNew, ma avvia un figlio per ogni elemento di una lista contenuta in un Global State del padre.

**TransitionX** è una forma più vecchia che unisce una Transition e l'avvio di un figlio. La piattaforma la esegue ancora, ma non puoi crearne una dal designer.

Vedi [Tipi di Transizione](../designer/transitions/types.md) per come appare ogni tipo nel designer.

---

## Self-loop

Una Transition può avere lo stesso stato sia come input che come output. Questa si chiama **self-loop**. I self-loop vengono comunemente usati per mantenere l'automaton in uno stato di attesa, interrogando una metric finché una condizione non cambia: ogni volta che il self-loop si attiva, l'action e la metric dello stato vengono eseguite di nuovo.

---

## Min Wait e Ignore Min Wait

Il **min wait** è il tempo, in secondi, che un automaton deve passare in uno stato prima di poterne uscire. Lo stabilisce chi avvia l'automaton: per un automaton figlio è il **Target Min Wait** della TransitionNew che lo ha avviato. Senza min wait, o con 0, nulla viene ritardato.

L'opzione **Ignore Min Wait** ne esenta una Transition: la Transition può attivarsi appena le sue condizioni sono soddisfatte. Abilitala quando una Transition deve reagire subito, per esempio a un errore.
