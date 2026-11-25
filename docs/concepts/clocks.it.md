# Clock e tempo

I clock consentono a un automaton di misurare il tempo trascorso e prendere decisioni in base a quanto tempo è rimasto in un determinato stato o in attesa che una condizione cambi.

---

## Cos'è un clock

Un clock è una variabile timer definita a livello di automaton. Ogni clock parte da zero quando l'automaton si avvia, e di nuovo a ogni azzeramento. Il suo valore corrente può essere letto in qualsiasi momento da una condizione di transizione.

I clock non hanno un'unità definita nell'automaton stesso — l'unità viene specificata quando si scrive il vincolo del clock: secondi, minuti o ore, secondi se non ne indichi nessuna. Il valore con cui confrontare è un numero, oppure un Global State dell'automaton come `T_max_wait`, così lo stesso automaton può usare timeout diversi per istanze diverse.

---

## Come vengono usati i clock

I clock vengono usati in due modi nelle transizioni.

Un **clock constraint** è una condizione che deve essere soddisfatta affinché la Transition si attivi. Ad esempio:

- `clk_retry >= 5 min` — la Transition si attiva solo dopo che sono trascorsi 5 minuti dall'ultimo azzeramento del clock
- `clk_handshake < T_handshake_timeout` — la Transition si attiva solo se il clock dell'handshake non ha ancora superato il timeout contenuto nel Global State `T_handshake_timeout`

Una Transition con più clock constraint si attiva solo quando sono soddisfatti tutti.

Un **clock reset** riavvia un clock quando una Transition si attiva. I reset vengono usati per misurare il tempo a partire da un evento specifico — ad esempio, azzerando un clock di retry ogni volta che un tentativo di connessione fallisce.

---

## Combinare vincoli e reset

I clock diventano più utili quando vincoli e reset vengono combinati su più transizioni.

Un pattern tipico è:

1. Una Transition azzera il clock all'ingresso in uno stato di attesa.
2. Una Transition si attiva se il clock è ancora entro un intervallo accettabile — mantenendo l'automaton nello stato di attesa.
3. Un'altra Transition si attiva quando il clock supera il timeout — spostando l'automaton in uno stato di errore o eccezione.

Questo pattern consente agli automata di implementare logiche di retry, watchdog timer e flussi di escalation senza alcuna schedulazione esterna.

---

## Definire i clock

I clock sono definiti a livello di automaton, non a livello di stato o Transition. Ogni clock usato in qualsiasi Transition di un automaton deve essere dichiarato nella lista dei clock dell'automaton.

Nello XAL Designer, i clock vengono gestiti dal pannello **Clock Vars** nella barra laterale sinistra (vedi [Variabili Clock](../designer/automata/clocks.md)).
