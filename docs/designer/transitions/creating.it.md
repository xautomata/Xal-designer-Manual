# Creare una Transizione

Le transizioni collegano gli stati e definiscono le condizioni in base alle quali l'automa si sposta da uno stato all'altro.

---

## Aggiungere una transizione

Fai clic sull'**icona riciclo** nella barra laterale sinistra per entrare in modalità creazione transizione. Poi disegna una connessione tra due stati nel canvas.

Il pannello di configurazione si apre sulla destra, mostrando la sezione **Metadata** con gli stati di input e output pre-compilati.

La transizione viene creata immediatamente. La sua configurazione può essere modificata nel pannello in qualsiasi momento.

---

## Impostare gli stati di input e output

Nella sezione **Metadata**, usa i menu a discesa **Input State** e **Output State** per impostare la sorgente e la destinazione della transizione.

Entrambi i campi sono obbligatori. Le opzioni disponibili sono tutti gli stati definiti nell'automa corrente.

!!! note
    Una transizione può avere lo stesso stato sia come input che come output. Questo crea un **self-loop** — l'automa rimane in quello stato fino a quando non viene soddisfatta una condizione diversa.

---

## Selezionare il tipo di transizione

Il tipo di transizione viene determinato automaticamente in base ai campi compilati:

- Se imposti un **Target Automaton** nella sezione Parameters, la transizione diventa una **TransitionNew** o **TransitionNewMulti**.
- Altrimenti, è una **Transition** standard.

Per una descrizione completa di ogni tipo e quando usarlo, vedi [Transition Types](types.md).

---

## Eliminare una transizione

Seleziona una transizione nel canvas facendo clic sul suo arco o sulla sua etichetta, poi fai clic sull'**icona cestino** nell'angolo in alto a destra del pannello di configurazione.
