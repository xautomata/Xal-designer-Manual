# Configurazione — API Key

Arianna richiede una API key di Anthropic per funzionare. La chiave viene salvata localmente nel tuo browser e non viene mai inviata ai server di Xautomata — viene usata esclusivamente per le chiamate dirette all'API di Claude.

---

## Inserire la chiave

Clicca sull'icona **Wand** nella barra laterale sinistra per aprire il pannello AI Assistant. Se non è configurata nessuna chiave, compare un prompt che ti chiede di inserirla.

Incolla la tua API key di Anthropic e clicca **Confirm**. La chiave viene validata immediatamente; se non è valida o non ha i permessi necessari, viene mostrato un messaggio di errore.

![Il prompt di inserimento API key nel pannello AI Assistant](../../images/designer/arianna/setup_api_key.png)
/// caption
Fig.1 - Il prompt di inserimento della API key (screenshot in attesa)
///

---

## Rimuovere la chiave

Per cancellare la chiave salvata — ad esempio prima di lasciare un computer condiviso — apri il pannello AI Assistant e clicca **Remove key**. Questa operazione disconnette Arianna e rimuove tutte le sessioni chat collegate a file del repository. Le sessioni collegate a file caricati localmente vengono conservate.

---

!!! warning "Sicurezza della chiave"
    La tua API key è salvata nel local storage del browser. Non usare Arianna su un computer condiviso o pubblico senza rimuovere la chiave al termine della sessione.
