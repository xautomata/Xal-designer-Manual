# Stati e Transitions

Gli stati e le Transitions sono i mattoni fondamentali di ogni automaton. Capire come lavorano insieme è indispensabile prima di usare lo XAL Designer.

---

## Stati

Uno stato rappresenta una condizione o fase nel ciclo di vita del processo monitorato.

Ogni automaton ha esattamente uno **stato iniziale** — lo stato in cui si trova quando viene istanziato per la prima volta. Può avere anche uno o più **stati finali** — stati che segnano la fine del ciclo di vita dell'automaton. Quando viene raggiunto uno stato finale, l'automaton smette di elaborare.

Tutti gli altri stati sono stati intermedi, che rappresentano le fasi attive del processo.

Nello XAL Designer, gli stati sono mostrati come nodi colorati sulla canvas:

| Colore | Significato |
|---|---|
| Verde | Stato iniziale |
| Rosso | Stato finale |
| Blu | Stato intermedio |

Ogni stato può essere opzionalmente associato a una **metric** o a un'**action**. Quando l'automaton entra in uno stato che ha una metric associata, inizia a osservare quella metric. Quando entra in uno stato con un'action, esegue quell'action.

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

## Tipi di Transition

Lo XAL Designer supporta quattro tipi di Transition.

**Transition** è il tipo standard. Si attiva quando viene osservato un valore di metric specifico, quando un clock constraint è soddisfatto, o incondizionatamente se non sono definite condizioni.

**TransitionNew** crea un nuovo automaton figlio quando si attiva. L'automaton padre continua nel suo stato di output mentre il figlio gira indipendentemente. Così si costruiscono le famiglie di automata.

**TransitionNewMulti** funziona come TransitionNew ma crea più istanze figlio contemporaneamente, una per ogni elemento in una variabile loop.

**TransitionX** è un tipo di Transition esteso che combina le caratteristiche dei tipi precedenti. Viene usato per scenari avanzati.

---

## Self-loop

Una Transition può avere lo stesso stato sia come input che come output. Questa si chiama **self-loop**. I self-loop vengono comunemente usati per mantenere l'automaton in uno stato di attesa, interrogando una metric finché una condizione non cambia.

---

## L'opzione Ignore Min Wait

Ogni Transition ha un tempo di attesa minimo che impedisce che si attivi troppo rapidamente. L'opzione **Ignore Min Wait** bypassa questa attesa, consentendo alla Transition di attivarsi immediatamente. Viene tipicamente abilitata quando una Transition deve reagire istantaneamente a un cambiamento.

!!! info
    Il comportamento esatto di Min Wait e la sua interazione con Ignore Min Wait in scenari specifici è tracciato come domanda aperta. Vedi `qa.md` — Q4.
