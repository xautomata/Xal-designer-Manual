# Tipi di Transizione

Il XAL Designer supporta più tipi di transizione. Il tipo determina cosa fa la transizione quando scatta e quali campi di configurazione sono disponibili.

---

## Transition

La transizione standard sposta l'automa da uno stato all'altro quando viene soddisfatta una condizione.

Supporta:

- un **Metric Value** — il valore specifico restituito dalla Metric dello stato di input che attiva la transizione
- **Clock Constraint** — condizioni basate sul tempo che devono essere soddisfatte
- **Clock Reset Variables** — Clock da azzerare quando la transizione scatta
- **Ignore Min Wait** — bypassa il tempo minimo di attesa, consentendo alla transizione di scattare immediatamente

Se non sono configurate condizioni, la transizione scatta incondizionatamente non appena l'automa entra nello stato di input.

Nel canvas, le transizioni standard sono visualizzate come **frecce continue**.

---

## TransitionNew

Una TransitionNew istanzia un nuovo automa figlio quando scatta, oltre a spostare l'automa nello stato di output.

Supporta:

- **Target Automaton** — l'automa da istanziare (selezionato tra gli automi definiti nel file corrente, o specificato come variabile di stato globale)
- **Target Min Wait** — un tempo di attesa minimo prima che l'automa figlio inizi l'elaborazione
- **Input Parameters** — valori passati dallo stato globale del genitore al figlio nel momento dell'istanziazione
- **Clock Constraint** e **Clock Reset Variables**

Nel canvas, gli archi TransitionNew sono visualizzati come **frecce tratteggiate** e l'etichetta mostra il nome dell'automa istanziato preceduto dal simbolo **+**.

!!! info
    Per una spiegazione concettuale di come interagiscono automi genitore e figlio, vedi [Automaton Families](../../concepts/families.md).

---

## TransitionNewMulti

TransitionNewMulti funziona come TransitionNew ma istanzia più istanze figlio in una singola operazione, iterando su una variabile lista.

Richiede un parametro **Loop Input** in aggiunta ai parametri di input standard — una singola variabile dello stato globale che contiene la lista di elementi su cui iterare.

---

## TransitionX

TransitionX è un tipo di transizione esteso che combina funzionalità di tutti gli altri tipi. È usato per scenari avanzati che richiedono sia l'istanziazione di un automa figlio sia la corrispondenza di un Metric Value sulla stessa transizione.

---

## Identificare i tipi di transizione nel canvas

| Aspetto | Tipo |
|---|---|
| Freccia continua | Transition |
| Freccia tratteggiata con etichetta **+** | TransitionNew o TransitionNewMulti |
| Etichetta che mostra un valore Metric | Qualsiasi tipo con condizione Metric Value |
| Etichetta che mostra un'espressione Clock | Qualsiasi tipo con Clock Constraint |
