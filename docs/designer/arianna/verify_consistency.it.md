# Verificare la consistenza

**Verify Consistency** esegue un confronto sistematico tra scenario e file XAL, controlla che ogni elemento descritto nello scenario abbia un costrutto corrispondente nell'XAL e segnala le eventuali discrepanze.

---

## Quando usarla

Esegui una verifica di consistenza:

- dopo aver generato uno scenario da un XAL esistente, per individuare eventuali errori di interpretazione nella generazione inversa
- dopo aver sincronizzato lo scenario, per confermare che l'aggiornamento abbia catturato tutte le modifiche
- prima di committare, come validazione finale che i due artefatti siano concordi

---

## Avviare la verifica

Clicca **Verify Consistency** nella barra delle azioni del tab scenario. Arianna esegue la verifica come operazione one-shot fuori dalla cronologia normale della chat e consegna il report come messaggio in chat.

Il report copre:

- stati e transizioni presenti in un artefatto ma assenti nell'altro
- nomi di azioni o metriche che non corrispondono tra scenario e XAL
- vincoli di clock descritti nello scenario ma non riflessi nelle transizioni
- variabili di global state elencate nello scenario che sono assenti o tipizzate diversamente nell'XAL

!!! info "Bias verso la coerenza"
    La verifica di consistenza di Arianna è calibrata per evitare falsi positivi. Quando una discrepanza è ambigua — per esempio, una descrizione che potrebbe corrispondere a più di un elemento XAL — non viene segnalata come errore. Vengono riportate solo le incongruenze chiare.

---

## Agire sul report

Leggi il report in chat e decidi come risolvere ogni punto. A seconda di quale artefatto deve essere considerato la fonte di verità:

- **Lo scenario è corretto → l'XAL deve cambiare:** modifica l'automa nel Designer e riesegui la pipeline di generazione, oppure correggi l'XAL manualmente.
- **L'XAL è corretto → lo scenario deve cambiare:** chiedi ad Arianna in chat di aggiornare le sezioni interessate, oppure usa [Sync Scenario](syncing_scenario.md).

Puoi chiedere ad Arianna di aiutarti a risolvere qualsiasi punto del report — il contesto della chat contiene già entrambi gli artefatti dopo la verifica.
