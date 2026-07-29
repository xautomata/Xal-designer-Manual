# Cos'è un Automaton

Un automaton è l'unità di elaborazione centrale della piattaforma XAUTOMATA. Definisce la logica operativa applicata a un sistema monitorato — cosa osservare, come reagire e quando attivare actions.

---

## L'idea di base

Un automaton modella il comportamento di un sistema reale come una sequenza di **stati** collegati da **transizioni**.

In ogni momento, un automaton si trova esattamente in uno stato. Quando viene soddisfatta una condizione specifica — come una metric che raggiunge un certo valore, o il superamento di una soglia temporale — l'automaton passa da uno stato all'altro. Questo movimento è chiamato **Transition**.

Ogni stato può essere associato a una **metric** da osservare o a un'**action** da eseguire. Le metrics leggono dati dal sistema monitorato. Le actions eseguono operazioni su di esso o su sistemi esterni.

---

## Un esempio concreto

Considera un server monitorato per l'utilizzo della CPU.

Un automaton per questo server potrebbe avere tre stati: **Normal**, **Warning** e **Critical**. Quando la metric della CPU riporta un valore superiore all'80%, l'automaton passa da Normal a Warning. Quando il valore supera il 95%, passa a Critical ed esegue un'action — ad esempio, aprendo un ticket di supporto.

Quando la CPU torna a un livello accettabile, l'automaton torna a Normal.

Questa logica gira continuamente, reagendo alle condizioni reali man mano che cambiano.

---

## Un automaton, molti oggetti

Una singola definizione di automaton può girare simultaneamente su molti oggetti monitorati. La stessa logica di monitoraggio della CPU si applica a ogni server dell'infrastruttura — ogni server ottiene la propria istanza in esecuzione dell'automaton, che traccia il proprio stato in modo indipendente.

Questo è ciò che rende gli automata scalabili: la logica viene definita una volta sola, e la piattaforma la applica ovunque sia necessario.

---

## Automata che collaborano

I processi complessi sono spesso modellati come **famiglie di automata** che collaborano. Un automaton padre orchestra il processo complessivo e crea automata figlio per gestire sotto-task specifici. Ogni figlio gira in modo indipendente, completa il proprio lavoro e riporta il suo stato finale al padre.

Ad esempio, un processo di patching IT potrebbe coinvolgere un automaton principale che coordina l'intera sequenza di patching, e automata figlio separati responsabili di rilocare i database, riciclare gli application pool e inviare notifiche a ogni step.

Questo approccio compositivo permette di suddividere logiche operative complesse in pezzi gestibili e riutilizzabili.

---

## Come gli automata si relazionano alla piattaforma

Gli automata non girano in isolamento. Sono connessi alla piattaforma XAUTOMATA tramite:

- **Metrics** — dati raccolti da oggetti e servizi infrastrutturali monitorati
- **Actions** — operazioni eseguite su sistemi esterni come piattaforme di ticketing, API o canali di notifica
- **Dispatchers** — regole che attivano notifiche e flussi di lavoro esterni in base alle transizioni di stato degli automata

Lo **XAL Designer** è lo strumento usato per creare e modificare gli automata visualmente, senza scrivere XML a mano.

!!! note
    Per una descrizione dettagliata del formato file XAL e di tutti gli elementi disponibili, consulta la sezione [XAL Reference](../reference/file_structure.md).
