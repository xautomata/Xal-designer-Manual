# Modifiche Non Committate

Ogni modifica apportata nel XAL Designer viene applicata immediatamente all'automa in memoria. Le modifiche non vengono salvate automaticamente nel repository — devi eseguire il commit esplicitamente quando sei pronto.

---

## Cosa conta come modifica

Il designer registra qualsiasi modifica all'automa come modifica non committata. Questo include:

- aggiunta, ridenominazione o eliminazione di stati
- aggiunta, modifica o eliminazione di transizioni
- modifica della configurazione di stati o transizioni nel pannello destro
- aggiornamento delle variabili di stato globale o delle variabili Clock
- spostamento di un nodo stato nel canvas

Anche riposizionare un nodo nel canvas — senza modificare alcuna configurazione — viene registrato come modifica, perché la posizione del nodo è memorizzata nel file XAL.

---

## L'indicatore a punto

Quando un automa ha modifiche non committate, un **punto** appare sul suo tab nella barra dei tab.

![Indicatore modifiche non committate](../../images/designer/publishing/dirty_indicator.png)
/// caption
Fig.1 - L'indicatore a punto su un tab con modifiche non committate
///

Il punto scompare dopo un commit riuscito.

---

## Chiudere un tab con modifiche non committate

Se provi a chiudere un tab che ha modifiche non committate, il designer mostra una finestra di dialogo di conferma:

> **Uncommitted changes — Are you sure you want to close?**

| Pulsante | Effetto |
|---|---|
| **Cancel** | Torna all'editor. Le modifiche vengono mantenute. |
| **Confirm** | Chiude il tab e scarta definitivamente tutte le modifiche non committate. |

!!! warning
    Le modifiche scartate non possono essere recuperate dall'interno del designer. Se hai bisogno di mantenere il tuo lavoro, esegui il commit prima di chiudere.

---

## Annullare le modifiche

Il canvas fornisce i pulsanti **undo** e **redo** in fondo all'area canvas (↺ ↻) per annullare le modifiche di layout come il riposizionamento dei nodi.

Per le modifiche di configurazione effettuate nel pannello destro — come rinominare uno stato o modificare una condizione di transizione — usa il pulsante **Reset** disponibile in ogni sezione di configurazione per ripristinare quella sezione al suo ultimo stato salvato.

Non esiste un annullamento globale per tutte le modifiche contemporaneamente. Se vuoi scartare tutte le modifiche non committate di un automa, chiudi il suo tab e conferma quando richiesto — poi riapri l'automa dal repository.

---

## Quando sei pronto a pubblicare

Una volta che hai revisionato le modifiche e sei soddisfatto, usa la funzione **Commit & Push** per pubblicarle nel repository.

Vedi [Commit and Push](commit.md) per il workflow completo di commit.
