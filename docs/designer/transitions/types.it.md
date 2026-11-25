# Tipi di Transizione

Il tipo di una transizione decide cosa fa quando scatta e quali campi mostra il suo pannello. Il titolo del pannello mostra il tipo.

Il tipo non si sceglie da una lista: in una transizione nuova segue i campi che compili (vedi [Creare una transizione](creating.md#il-tipo-di-transizione)).

---

## Transition

La transizione standard sposta l'automa dallo stato di input allo stato di output.

Il suo pannello mostra:

- **Metric Value**: il valore che la Metric dello stato di input deve restituire;
- **Ignore Min Wait**: fa scattare la transizione senza aspettare il min wait (vedi sotto);
- **Clock Constraints** e **Clock Reset Variables**.

Una transizione senza Metric Value e senza vincoli Clock scatta appena l'automa è nello stato di input.

Per questi campi vedi [Metric Value e Clock](metric_clocks.md).

---

## TransitionNew

Una TransitionNew avvia un **automa figlio** e sposta il padre nello stato di output.

Il suo pannello mostra:

- **Target File** e **Target Automaton**: l'automa da avviare;
- **Target Min Wait**: il min wait del figlio, in secondi (vedi sotto);
- le righe **Input Parameter**: i valori passati al figlio quando parte;
- **Clock Constraints** e **Clock Reset Variables**.

Una TransitionNew non ha Metric Value: scatta solo sui suoi vincoli Clock, e il min wait non la ritarda mai.

Per il target e i parametri vedi [Istanziare automi figli](transition_new.md).

!!! info
    Per come lavorano insieme automi padre e figli, vedi [Famiglie di automata](../../concepts/families.md).

---

## TransitionNewMulti

Una TransitionNewMulti funziona come una TransitionNew, ma avvia **un figlio per ogni elemento di una lista**. La lista è un Global State del padre, scelto nella riga del ciclo di **Input Parameter**.

---

## TransitionX

TransitionX è una forma più vecchia che mette in un solo elemento una transizione e l'avvio di un figlio. L'interprete la esegue ancora e il designer la disegna come una freccia semplice, ma **non puoi crearne una** dal designer.

Per ottenere lo stesso comportamento, disegna una Transition e una TransitionNew tra gli stessi due stati. All'esecuzione diventano una sola transizione: scatta sul Metric Value della Transition e usa i vincoli e i reset Clock della TransitionNew.

---

## Min Wait e Ignore Min Wait

Il **min wait** è il tempo, in secondi, che un automa deve passare in uno stato prima di poterne uscire. Lo stabilisce chi avvia l'automa: per un automa figlio è il **Target Min Wait** della TransitionNew che lo ha avviato. Senza min wait, o con 0, nulla viene ritardato.

**Ignore Min Wait** ne esenta una transizione: la transizione può scattare appena le sue condizioni sono vere.

---

## I tipi di transizione nel canvas

| Aspetto | Tipo |
|---|---|
| Freccia continua | Transition |
| Freccia tratteggiata, senza ✚ | Transition con **Ignore Min Wait** attivo |
| Freccia tratteggiata, etichetta **✚** e l'automa target | TransitionNew |
| Freccia tratteggiata, etichetta **✚✚** e l'automa target | TransitionNewMulti |
| Freccia continua, senza etichetta | TransitionX |

L'etichetta di una Transition mostra il suo Metric Value, più il simbolo di un orologio per un vincolo Clock e **↺** per un reset. Passa sopra una freccia per vedere i dettagli.
