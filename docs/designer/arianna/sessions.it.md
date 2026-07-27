# Sessioni chat

Il pannello AI Assistant può contenere più sessioni chat contemporaneamente. Ogni sessione ha la propria cronologia, il proprio scenario e il proprio contesto XAL — passare dall'una all'altra è istantaneo.

---

## Il pannello sessioni

Clicca l'**icona sessioni** nell'intestazione della chat per aprire il pannello sessioni. Elenca tutte le sessioni attive, divise in due gruppi:

- **Sessioni file** — collegate a un file XAL specifico nel repository
- **Sessioni di sistema** — sessioni sempre presenti per l'assistenza generale (vedi sotto)

Clicca qualsiasi sessione nell'elenco per passarvi. Se la sessione di destinazione ha uno scenario, il suo tab scenario si apre nell'editor; il tab scenario precedente viene chiuso prima (con un prompt di salvataggio se era in modalità modifica con modifiche non salvate).

---

## Gestire le sessioni file

### Creare una sessione
Clicca con il tasto destro su qualsiasi file `.xal` o `.scenario.md` nel **Repository Explorer** e seleziona **New chat**. Una sola sessione per file — se esiste già una sessione per quel file, compare **Open with chat** e la sessione esistente viene riattivata.

### Resettare una sessione
Sono disponibili due opzioni di reset dal menu della sessione:

| Opzione | Cosa rimuove | Cosa conserva |
|---|---|---|
| **Reset history** | I messaggi della chat | Il file collegato, lo scenario, il tab scenario |
| **Reset everything** | I messaggi + lo scenario + il file `.scenario.md` | Solo il file collegato |

Usa *Reset history* per liberare token di contesto quando la conversazione è diventata lunga ma lo scenario è ancora valido. Usa *Reset everything* per ricominciare da capo sullo stesso file.

### Eliminare una sessione
Seleziona **Delete** dal menu della sessione. La sessione e la sua cronologia vengono rimosse; i file `.scenario.md` e `.xal` nel repository non vengono toccati.

---

## Sessioni di sistema

Due sessioni sono sempre presenti nel pannello e non possono essere eliminate né rinominate. Vengono create automaticamente al primo avvio e ripristinate se la memoria del browser viene svuotata.

| Sessione | Cosa fa |
|---|---|
| **Interface Guide** | Risponde a domande sull'interfaccia del XAL Designer — pannelli, pulsanti, flussi di lavoro e navigazione |
| **XAL Guide** | Risponde a domande sul linguaggio XAL — sintassi, tipi di stati e transizioni, azioni, metriche e parametri |

Queste sessioni non hanno un file collegato né un tab scenario. Operano indipendentemente dal file su cui stai lavorando — usale per cercare informazioni senza interrompere la sessione corrente.

---

## Modalità chat

Ogni sessione file ha un **selettore di modalità** nell'intestazione della chat. La modalità controlla le istruzioni che Arianna riceve e il tipo di output che produce. La modalità corretta viene selezionata automaticamente in base allo stato degli artefatti, ma puoi cambiarla manualmente in qualsiasi momento.

| Modalità | Auto-selezionata quando | Cosa fa Arianna |
|---|---|---|
| **Build scenario** | Non esiste ancora nessuno scenario | Ti guida nella descrizione dell'automa e genera uno scenario strutturato |
| **Update scenario** | Lo scenario esiste, l'XAL è assente o non aggiornato | Ti aiuta ad affinare lo scenario prima di (ri)generare l'XAL |
| **Consulenza** | Scenario e XAL sono allineati | Risponde a domande operative sull'automa — cosa significa uno stato, cosa attiva una transizione, come regolare una soglia — senza modificare gli artefatti a meno che tu non lo chieda esplicitamente |

!!! info
    Cambiare modalità non cancella la cronologia della conversazione. Il prossimo messaggio che invii utilizzerà semplicemente le istruzioni della nuova modalità.

---

## Flussi guidati

Diverse azioni in Arianna attivano **flussi guidati** — sequenze di passi che Arianna esegue automaticamente o ti invita a eseguire, senza che tu debba navigare l'interfaccia manualmente.

### Flusso post-generazione (Generate scenario from XAL)

Dopo aver generato uno scenario da un XAL esistente, Arianna invia in chat un prompt che ti invita a verificare la consistenza, con un pulsante **Verify Consistency** inline nel messaggio. Dopo la verifica, un secondo messaggio stabilisce la direzione di lavoro per la sessione (vedi [Generare uno scenario dall'XAL](scenario_from_xal.md)).

### Prompt post-sync

Dopo il completamento di **Sync Scenario**, Arianna invia un breve messaggio in chat suggerendo una verifica di consistenza — un promemoria che la sincronizzazione aggiorna lo scenario per corrispondere all'XAL, ma non garantisce che nessun dettaglio vada perso.

### Retry automatico in caso di validazione fallita

Quando **Generate XAL** o **Update XAL** produce un XAL non valido, Arianna riprova automaticamente fino a due volte (tre tentativi totali), passando a Claude la storia completa degli errori accumulati ad ogni tentativo. Il retry avviene in silenzio — vedi solo il messaggio di stato aggiornarsi con il numero del tentativo corrente.

Se tutti e tre i tentativi falliscono, il flusso di fallback mette a disposizione:

- gli errori di validazione in chiaro, per capire cosa è andato storto
- un pulsante **Download XAL** per l'ultimo file generato — spesso quasi corretto
- un pulsante **Download .dot** (nella sezione *Advanced*) per chi vuole correggere il sorgente GraphViz manualmente

### Pulsanti azione inline

In alcuni messaggi chat — in particolare quelli generati dai flussi guidati — Arianna include **pulsanti azione inline** direttamente nel messaggio. Questi avviano la corrispondente azione del Designer (come *Verify Consistency*) senza che tu debba trovare il pulsante nel tab scenario. Cliccare il pulsante inline ha esattamente lo stesso effetto che cliccare il pulsante nell'interfaccia.
