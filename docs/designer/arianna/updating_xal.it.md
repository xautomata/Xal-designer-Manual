# Aggiornare l'XAL

Quando lo scenario cambia dopo che l'XAL è già stato generato, usa **Update XAL** per propagare le modifiche al file esistente senza rigenerare da zero.

---

## Quando compare Update XAL

Il pulsante nella barra delle azioni del tab scenario cambia etichetta e colore in base allo stato di allineamento dei due artefatti:

| Etichetta pulsante | Colore | Significato |
|---|---|---|
| **Generate XAL** | Verde | Non esiste ancora un file XAL, oppure l'XAL esistente è stato caricato manualmente senza una storia di generazione |
| **Update XAL** | Verde | Scenario e XAL sono allineati — il pulsante è disponibile per rigenerare se necessario |
| **Update XAL** | Arancione | Lo scenario è stato modificato dall'ultima generazione — l'XAL non è aggiornato |

Il colore arancione è un segnale visivo che i due artefatti non sono sincronizzati. Clicca **Update XAL** per riallineare l'XAL allo scenario.

---

## Cosa cambia rispetto a Generate XAL

Quando genera per la prima volta, Arianna costruisce il grafo dell'automa da zero. In fase di aggiornamento, riceve il **grafo esistente** insieme al nuovo scenario e produce solo le modifiche necessarie — preservando stati, transizioni e identificatori di nodo invariati.

Questo approccio chirurgico evita ristrutturazioni non necessarie di un automa che potrebbe essere già parzialmente o completamente corretto.

Il contesto che Arianna riceve dipende da ciò che è disponibile nella sessione corrente:

| Disponibile | Contesto passato ad Arianna |
|---|---|
| File `.dot` di una generazione precedente | Nuovo scenario + `.dot` esistente (preferito — massimo contesto strutturale) |
| Nessun `.dot`, ma XAL presente | Nuovo scenario + XAL esistente (usato come riferimento strutturale) |

Il file `.dot` viene conservato internamente dalla sessione dopo ogni generazione. Non è visibile nel repository explorer, ma viene usato automaticamente da **Update XAL** quando disponibile.

---

## Dopo l'aggiornamento

Il file XAL viene sovrascritto con la nuova versione e ricaricato nel grafo. L'indicatore di allineamento torna verde.

Se l'aggiornamento introduce errori di validazione, si applicano lo stesso [retry automatico e il fallback con download](generating_xal.md#se-tutti-i-tentativi-falliscono) previsti per una generazione iniziale.
