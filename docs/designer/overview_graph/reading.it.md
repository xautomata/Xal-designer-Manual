# Leggere il Process Overview

Il tab **Overview** mostra un grafico di tutti gli automi nel repository caricato e le relazioni tra loro.

Questa vista offre una visione d'insieme dell'intero sistema di automazione — quali automi esistono, come sono collegati e quali fanno riferimento ad automi in altri repository.

![Process Overview](../../images/designer/overview_graph/overview.png)
/// caption
Fig.1 - Il grafico del Process Overview
///

---

## Forme dei nodi

Ogni nodo nel grafico rappresenta un automa. La forma del nodo indica dove l'automa è definito.

| Forma | Significato |
|---|---|
| Rettangolo arrotondato (card) | Automa definito nel repository corrente |
| Nuvola | Automa referenziato per nome del file, path non risolto |
| Ellisse | Automa referenziato solo per nome (variabile Global State) |

I nodi che fanno riferimento ad automi in repository esterni sono visualizzati con un bordo rosso. Questi automi esistono e girano a livello di piattaforma, ma i loro file sorgente non fanno parte del repository corrente.

---

## Colori del bordo dei nodi

Il colore del bordo di un nodo card riflette il suo ruolo nel grafico delle correlazioni.

| Colore bordo | Significato |
|---|---|
| Verde | Solo connessioni in uscita — un automa sorgente |
| Rosso | Solo connessioni in ingresso — un automa foglia o un riferimento esterno |
| Blu | Connessioni sia in ingresso che in uscita — un automa intermedio |

---

## Interagire con il grafico

**Clic singolo** su un nodo per evidenziare tutte le sue connessioni. Gli archi collegati a quel nodo diventano più spessi e mostrano le loro etichette.

**Doppio clic** su un nodo per aprire l'automa corrispondente nel canvas come nuovo tab.

Il grafico supporta lo scorrimento e lo zoom. I nodi possono essere trascinati per riorganizzare il layout — le loro posizioni vengono mantenute durante la sessione ma vengono azzerate al ricaricamento della pagina.

---

## Filtri

Usa i controlli nell'angolo in alto a destra per mostrare o nascondere specifici tipi di correlazione e per attivare/disattivare la visibilità dei nodi orfani — automi che non hanno connessioni con altri automi nella vista corrente.

!!! info
    Per una descrizione dei tre tipi di correlazione e di cosa rappresentano, vedi [Correlation Types](correlation_types.md).
