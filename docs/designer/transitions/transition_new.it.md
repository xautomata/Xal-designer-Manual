# Istanziare Automi Figli (TransitionNew)

Una **TransitionNew** fa due cose quando scatta: sposta l'automa nello stato di output e istanzia un nuovo automa figlio. Questo è il meccanismo usato per costruire famiglie di automi.

---

## Configurare una TransitionNew

Quando imposti un **Target Automaton** nella sezione Parameters di una transizione, la transizione diventa automaticamente una TransitionNew.

![Pannello TransitionNew](../../images/designer/transitions/transition_new.png)
/// caption
Fig.1 - Il pannello di configurazione della TransitionNew
///

---

## Target Automaton

Seleziona l'automa da istanziare dal menu a discesa **Target Automaton**. Le opzioni disponibili includono:

- Tutti gli automi definiti nel file XAL corrente
- Variabili di stato globale, se l'automa target viene determinato dinamicamente a runtime

L'automa selezionato viene istanziato come nuova istanza indipendente quando la transizione scatta.

---

## Target Min Wait

Il campo **Target Min Wait** imposta un ritardo minimo (in secondi) prima che l'automa figlio inizi l'elaborazione dopo essere stato istanziato. È utile quando il figlio deve aspettare che il genitore si stabilizzi prima di iniziare a leggere lo stato del genitore.

---

## Parametri di input

La sezione **Parameters** consente di passare valori dallo stato globale dell'automa genitore al figlio nel momento in cui viene creato.

Ogni mappatura di parametro consiste in:

| Campo | Descrizione |
|---|---|
| **Target Variable** | Il nome della variabile nello stato globale dell'automa figlio |
| **Local Variable** | Il nome della variabile nello stato globale dell'automa genitore da cui leggere |

Fai clic su **+** per aggiungere una mappatura di parametro. Fai clic su **×** per rimuoverne una.

!!! info
    L'automa figlio deve avere le variabili corrispondenti definite nel proprio stato globale affinché i valori vengano ricevuti correttamente.

---

## Monitorare il figlio

Una volta che il figlio è in esecuzione, il genitore ne monitora l'avanzamento tramite una Metric. La Metric legge lo stato globale del figlio usando una query SQL-like che fa riferimento all'automa figlio per nome ed etichetta di gruppo.

Man mano che il figlio transita tra i suoi stati e raggiunge infine uno stato finale, la Metric del genitore restituisce il valore corrispondente e scatta la transizione appropriata.

Per una panoramica concettuale di questo pattern, vedi [Automaton Families](../../concepts/families.md).

---

## Rappresentazione nel canvas

Gli archi TransitionNew appaiono come **frecce tratteggiate** nel canvas. L'etichetta mostra il nome dell'automa istanziato preceduto dal simbolo **+**.

Quando più archi TransitionNew escono dallo stesso stato, ciascuno istanzia un automa figlio diverso — tutti vengono creati quando il genitore entra in quello stato.
