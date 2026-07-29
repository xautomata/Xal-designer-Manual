# L'Interfaccia

L'interfaccia del XAL Designer è organizzata in quattro aree principali che collaborano durante una sessione di modifica.

![Panoramica interfaccia XAL Designer](../../images/designer/getting_started/interface.png)
/// caption
Fig.1 - L'interfaccia del XAL Designer con un automa aperto (screenshot in arrivo)
///

---

## Barra superiore

La barra superiore è sempre visibile e fornisce accesso ai controlli globali.

| Elemento | Descrizione |
|---|---|
| **Repository** | Mostra il repository attualmente collegato. Fare clic per cambiarlo. |
| **Branch** | Seleziona il branch su cui lavorare. |
| **Pulsante Upload / Commit** | Quando collegato a un VCS: apre la finestra di dialogo Commit & Push. Quando si lavora in modalità browser: apre la finestra di dialogo per il caricamento del file. |
| **Run-time Instance** | Riservato per uso futuro — permetterà di connettersi a un'istanza live della piattaforma. |

---

## Barra laterale sinistra

La barra laterale fornisce accesso a tutti i pannelli e gli strumenti di modifica. Fare clic su un'icona per aprire il pannello corrispondente.

| Icona | Pannello | Descrizione |
|---|---|---|
| File | Repository Explorer | Sfoglia la struttura di file e automi del repository collegato |
| Robot | Automata Management | Crea, rinomina ed elimina automi |
| Globo | Global State Variables | Gestisce le variabili di stato globale dell'automa attivo |
| Orologio | Clock Variables | Gestisce le variabili Clock dell'automa attivo |
| Microchip | New State | Aggiunge un nuovo stato all'automa attivo |
| Riciclo | New Transition | Aggiunge una nuova transizione all'automa attivo |
| Store | Marketplace | *(Coming soon)* |
| Bacchetta | AI Assistant | Apre il pannello chat di Arianna |

---

## Canvas

Il canvas occupa l'area centrale dell'interfaccia.

Mostra una delle due viste in base al tab attivo:

- **Overview** — il grafico delle correlazioni che mostra tutti gli automi nel repository e le loro relazioni
- **Diagramma automa** — il grafico interattivo di un automa specifico, con gli stati come nodi e le transizioni come archi

È possibile scorrere il canvas facendo clic e trascinando su un'area vuota, e ingrandire o ridurre con la rotella del mouse. I singoli nodi possono essere trascinati per riposizionarli.

In fondo al canvas, due pulsanti forniscono le funzioni **undo** e **redo** per le modifiche di layout.

---

## Pannello destro

Il pannello destro mostra il modulo di configurazione per l'elemento attualmente selezionato nel canvas.

Si apre automaticamente quando si fa clic su un nodo stato o un arco transizione. Si chiude facendo clic su un'area vuota del canvas oppure premendo il pulsante **×** nell'intestazione del pannello.

Il contenuto del pannello cambia in base all'elemento selezionato:

- **Stato selezionato** — mostra le sezioni Metadata, Action e Metric dello stato
- **Transizione selezionata** — mostra le sezioni Metadata, Clocks e Parameters della transizione

Le modifiche apportate nel pannello vengono applicate immediatamente all'automa, con un breve ritardo di debounce.

---

## Tab

Ogni automa aperto appare come tab nella barra dei tab, accanto al tab persistente **Overview**.

Un **indicatore a punto** su un tab segnala che l'automa ha modifiche non ancora committate. Fare clic su un tab porta il canvas su quell'automa. Fare clic sulla **×** di un tab lo chiude — se ci sono modifiche non committate, appare prima una finestra di dialogo di conferma.
