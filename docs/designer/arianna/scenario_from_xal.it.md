# Generare uno scenario dall'XAL

Se hai un file XAL esistente senza uno scenario associato, Arianna può ricostruirne uno leggendo la struttura dell'automa.

---

## Quando usarla

Questo flusso è utile quando:

- stai lavorando con un file XAL legacy creato manualmente o con un altro strumento
- hai ricevuto un file XAL e vuoi una descrizione leggibile di cosa fa
- vuoi avviare una sessione di consulenza su un automa esistente

---

## Avviare il flusso

Apri una sessione chat per il file XAL (tasto destro → **Open chat** o **New chat**). Se il file non ha ancora uno scenario, l'area chat mostra una singola call-to-action:

**Generate scenario from XAL**

Cliccala. Arianna legge l'intero contenuto XAL e genera uno scenario strutturato. Il file satellite `.scenario.md` viene creato e il tab scenario si apre.

---

## Il flusso guidato di verifica

Ricostruire uno scenario dall'XAL è un'operazione inversa — Arianna inferisce l'intenzione dalla struttura, il che può introdurre imprecisioni, soprattutto con automi complessi che contengono logiche non ovvie.

Per aiutarti a individuare questi problemi immediatamente, Arianna avvia automaticamente un flusso guidato dopo la generazione dello scenario:

**Step 1 — Prompt post-generazione**

Arianna invia un messaggio in chat suggerendo che il primo scenario generato potrebbe contenere imprecisioni, con un pulsante **Verify Consistency** inline nel messaggio. Non devi cercare il pulsante nel tab scenario — è direttamente in chat.

**Step 2 — Verifica di consistenza**

Clicca il pulsante inline per avviare la verifica. Il report appare in chat come di consueto.

**Step 3 — Messaggio di direzione**

Dopo il report, Arianna invia un secondo messaggio che stabilisce la direzione di lavoro per la sessione:

> *"L'XAL è la fonte di verità. Le incongruenze rilevate vanno risolte modificando lo scenario — non l'XAL. Chiedi pure aiuto per affinarlo."*

Questo messaggio rimane nella cronologia della chat e informa tutte le risposte successive: quando chiedi ad Arianna di correggere una discrepanza, proporrà modifiche allo scenario, lasciando l'XAL intatto.

---

## Affinare lo scenario

Una volta completata la verifica di consistenza, affina lo scenario descrivendo le correzioni in chat. L'XAL rimane invariato per tutta la durata di questo flusso — l'obiettivo è produrre uno scenario che descriva accuratamente ciò che l'XAL fa, non cambiare l'XAL.

Quando lo scenario è accurato, puoi passare alla modalità [Consulenza](sessions.md#modalità-chat) per fare domande operative sull'automa, oppure committare entrambi i file nel repository.
