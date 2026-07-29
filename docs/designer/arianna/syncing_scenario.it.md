# Sincronizzare lo scenario

Quando modifichi un automa direttamente nel Designer — aggiungendo stati, cambiando transizioni o aggiustando parametri — l'XAL cambia ma lo scenario no. Usa **Sync Scenario** per aggiornare lo scenario in modo che rifletta lo stato corrente dell'XAL.

---

## Quando usarla

Sync Scenario è utile quando:

- hai affinato il grafo dell'automa manualmente dopo una generazione e vuoi che lo scenario rimanga una descrizione accurata
- vuoi mantenere i due artefatti allineati prima di committare

!!! note "Generare uno scenario per un file che non ne ha"
    Se hai un file XAL senza uno scenario associato, usa [Genera scenario da XAL](scenario_from_xal.md) — Sync Scenario richiede che il tab scenario sia già presente e aperto.

---

## Avviare la sincronizzazione

Clicca **Sync Scenario** nella barra delle azioni del tab scenario. Arianna legge il contenuto XAL corrente e aggiorna solo le parti dello scenario che corrispondono a ciò che è cambiato — non riscrive l'intero documento.

La precisione dell'aggiornamento dipende da ciò che è disponibile:

| Situazione | Cosa riceve Arianna | Risultato |
|---|---|---|
| L'XAL è stato modificato dall'ultima generazione | Scenario esistente + diff delle modifiche XAL | Aggiornamento mirato — cambiano solo le sezioni dello scenario interessate |
| Primo sync della sessione (nessuna baseline di diff) | Scenario esistente + intero XAL corrente | Aggiornamento con contesto completo — Arianna usa lo scenario esistente come riferimento di stile e struttura |

---

## Dopo la sincronizzazione

Il tab scenario viene aggiornato con il nuovo contenuto e il file `.scenario.md` viene sovrascritto. Arianna invia poi un breve messaggio in chat suggerendo una verifica di consistenza — un passaggio consigliato dopo ogni sync per confermare che nessun dettaglio sia andato perso.

!!! info "L'XAL è la fonte di verità durante un sync"
    Sync Scenario porta sempre lo scenario verso l'XAL, mai il contrario. Se vuoi aggiornare l'XAL in base a uno scenario modificato, usa invece [Update XAL](updating_xal.md).
