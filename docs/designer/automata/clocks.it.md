# Variabili Clock

Le variabili Clock sono identificatori di timer utilizzati nelle transizioni per misurare il tempo trascorso e applicare condizioni basate sul tempo.

Per visualizzare e gestire le variabili Clock, fai clic sull'**icona orologio** nella barra laterale sinistra. Il pannello mostra tutti i Clock definiti per l'automa attualmente attivo.

![Pannello Clock Variables](../../images/designer/automata/clock_vars_panel.png)
/// caption
Fig.1 - Il pannello Clock Variables
///

---

## Aggiungere un Clock

Fai clic su **+** o sul pulsante di aggiunta per creare un nuovo Clock. Un Clock ha un unico campo obbligatorio: il suo **nome**. Questo nome viene usato nei vincoli Clock e nei reset Clock sulle transizioni.

A differenza delle variabili di stato globale, i Clock non hanno tipo né valore predefinito — sono puramente contatori di tempo.

---

## Eliminare un Clock

Fai clic sull'**icona cestino** sulla scheda di un Clock per rimuoverlo. Prima di eliminare un Clock, assicurati che nessuna transizione nell'automa vi faccia riferimento in un vincolo Clock o in un reset Clock.

---

## Usare i Clock nelle transizioni

Una volta definito un Clock, diventa disponibile nella sezione **Clocks** del pannello di configurazione della transizione:

- **Clock Constraint** — aggiunge una condizione come `clk_retry >= 5 min` che deve essere soddisfatta affinché la transizione scatti
- **Clock Reset Variables** — seleziona i Clock da azzerare quando la transizione scatta

Per una spiegazione completa di come i Clock guidano la logica basata sul tempo, vedi [Clocks and Time](../../concepts/clocks.md).
