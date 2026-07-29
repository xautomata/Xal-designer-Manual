# Generare uno scenario

Lo scenario è la descrizione strutturata in linguaggio naturale di un automa — il ponte tra la tua intenzione e il file XAL. Arianna ti aiuta a costruirlo attraverso un flusso conversazionale.

---

## Aprire una sessione chat

Puoi avviare la generazione di uno scenario da due punti di ingresso:

**Da una sessione libera** — apri il pannello sessioni nell'intestazione della chat e crea una nuova sessione. Questa sessione parte senza un file collegato. Descrivi il processo, genera lo scenario, e Arianna assegnerà un nome file suggerito quando consegna il risultato. Il file `.xal` verrà creato più tardi, quando clicchi **Generate XAL**.

**Da un file esistente** — clicca con il tasto destro su un file `.xal` nel **Repository Explorer** e seleziona **New chat** (o **Open chat** se esiste già una sessione). La sessione è pre-collegata al file e pre-intestata con il nome del file. Il file non deve avere contenuto.

---

## Descrivere il processo

Digita una descrizione del comportamento dell'automa nel campo di testo della chat. Non ci sono requisiti di formato — il linguaggio naturale funziona bene. Includi:

- quale condizione o evento l'automa monitora
- quali stati può assumere
- quali azioni o notifiche innesca
- eventuali vincoli di tempo o soglie

Puoi descrivere tutto in una volta o costruire lo scenario attraverso più scambi. Arianna fa domande di approfondimento quando la descrizione è ambigua o incompleta.

---

## Generare lo scenario

Quando Arianna considera la descrizione sufficiente per produrre uno scenario minimale, il pulsante **Generate Scenario** diventa attivo nella toolbar della chat.

Clicca **Generate Scenario**. Arianna produce uno scenario strutturato in formato Markdown. Contemporaneamente:

- il file satellite `.scenario.md` viene creato nel repository explorer, accanto al file `.xal`
- il **tab scenario** si apre nell'editor, mostrando lo scenario renderizzato

![Il tab scenario nell'editor dopo la generazione](../../images/designer/arianna/generating_scenario_result.png)
/// caption
Fig.1 - Il tab scenario dopo la generazione
///

---

## Affinare lo scenario

Leggi lo scenario nel tab. Se qualcosa è errato o mancante, descrivi la modifica in chat e chiedi ad Arianna di aggiornarlo. Ogni rigenerazione sovrascrive il file `.scenario.md` e aggiorna il tab.

!!! info "Lo scenario è la fonte di verità"
    Durante la fase di generazione, lo scenario è ciò su cui Arianna lavora. L'XAL non è ancora stato generato — affina lo scenario finché non descrive accuratamente il comportamento desiderato prima di procedere.

Quando lo scenario è pronto, vai a [Generare l'XAL](generating_xal.md).
