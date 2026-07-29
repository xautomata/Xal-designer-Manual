# Creare uno Stato

Gli stati sono i nodi del diagramma dell'automa. È possibile creare un nuovo stato in qualsiasi momento mentre un automa è aperto nel canvas.

---

## Aggiungere uno stato

Fai clic sull'**icona microchip** nella barra laterale sinistra. Un nuovo nodo stato denominato `NewNode` appare immediatamente nel canvas e il pannello di configurazione si apre sulla destra.

![Nuovo stato nel canvas](../../images/designer/states/new_state.png)
/// caption
Fig.1 - Uno stato appena creato nel canvas
///

Lo stato viene creato immediatamente — non c'è un passaggio di conferma separato. Non appena digiti un nome, il nodo si aggiorna nel canvas in tempo reale.

---

## Assegnare un nome allo stato

Nel campo **Name** della sezione Metadata, sostituisci `NewNode` con l'identificatore scelto. I nomi degli stati devono essere univoci all'interno dell'automa.

Se inserisci un nome già in uso, il pannello mostra un messaggio di validazione che chiede di scegliere un nome diverso.

---

## Impostare il tipo di stato

Usa il selettore **Type** per contrassegnare lo stato come **Initial** o **Final**.

- Seleziona **Initial** per rendere questo lo stato iniziale dell'automa. Un automa può avere un solo stato iniziale.
- Seleziona **Final** per contrassegnarlo come stato terminale. Un automa può avere più stati finali. Quando l'automa raggiunge uno stato finale, interrompe l'elaborazione.

Lascia entrambe le opzioni deselezionate per gli stati intermedi.

---

## Spostare gli stati nel canvas

Trascina un nodo stato per riposizionarlo nel canvas. Il layout non è vincolato — posiziona i nodi dove rende il diagramma più leggibile.

!!! note
    Spostare un nodo conta come modifica. L'indicatore del tab mostrerà un punto, segnalando modifiche non committate.

---

## Eliminare uno stato

Per eliminare uno stato, selezionalo nel canvas e fai clic sull'**icona cestino** nell'angolo in alto a destra del pannello di configurazione.

!!! warning
    Eliminare uno stato rimuove anche tutte le transizioni a esso collegate. Questa operazione non può essere annullata all'interno della sessione del designer.
