# Struttura del file XAL

Un file XAL è un documento XML che contiene una o più definizioni di automi. Tutti gli automi in un file condividono lo stesso namespace e possono referenziarsi reciprocamente per nome.

---

## Elemento radice

L'elemento radice di ogni file XAL è `<XAL>`. Contiene uno o più elementi `<Automaton>`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<XAL>
  <Automaton Id="MyAutomaton">
    ...
  </Automaton>
  <Automaton Id="AnotherAutomaton">
    ...
  </Automaton>
</XAL>
```

L'attributo opzionale `parameter` su `<XAL>` indica dove l'interprete trova il codice Java delle action e delle metric del file. È un elenco separato da `;`: ogni voce è un file `.jar`, oppure una cartella che termina con `/`, di cui si usano tutti i file `.jar`.

```xml
<XAL parameter="/opt/xautomata/lib/connectors.jar;/opt/xautomata/lib/custom/">
```

---

## Struttura dell'automa

Ogni elemento `<Automaton>` contiene i seguenti elementi figlio, tutti allo stesso livello e in qualsiasi ordine:

| Elemento | Obbligatorio | Descrizione |
|---|---|---|
| `<Functions>` | Sì | Definisce le metric e le action utilizzate dall'automa |
| `<States>` | Sì | Elenca tutti gli stati |
| `<InitialState>` | Sì | Dichiara lo stato iniziale |
| `<Transitions>` | Sì | Contiene tutte le definizioni di transizione |
| `<GlobalState>` | No | Dichiara le variabili condivise |
| `<Clocks>` | No | Dichiara le variabili degli orologi |
| `<FinalStates>` | No | Elenca gli stati terminali |

---

## Ordine di elementi e attributi

L'ordine di elementi e attributi non ha effetto sull'interprete. Il XAL Designer li scrive sempre nello stesso ordine, così due versioni di un file sono facili da confrontare:

- dentro `<Automaton>`: `GlobalState`, `Clocks`, `Functions`, `States`, `InitialState`, `FinalStates`, `Transitions`;
- dentro `<Functions>`: prima tutte le action, poi tutte le metric;
- dentro `<Transitions>`: prima tutti gli elementi `Transition`, poi `TransitionNew`, `TransitionNewMulti` e `TransitionX`.

Quando salvi un file scritto a mano o con un altro strumento, il designer lo riscrive in questo ordine. Il contenuto non cambia.

---

## Denominazione e organizzazione dei file

I file XAL usano l'estensione `.xal`. Più automi vengono tipicamente raggruppati in un singolo file quando formano una famiglia — un automa padre e i suoi figli correlati.

I file sono organizzati in una struttura di directory all'interno del repository. Gli elementi `TransitionNew` referenziano gli automi figlio per nome di file (`Path`) e ID automa (`Type`). Vedi [Transitions](transitions.md#transitionnew).

---

## Validazione

I file XAL sono validati dalla piattaforma XAUTOMATA al momento del caricamento, rispetto allo schema del linguaggio XAL. Un file che non supera la validazione non viene caricato e nessuno dei suoi automi viene eseguito. Rendono il file non valido, per esempio, elementi o attributi che lo schema non conosce, un elemento obbligatorio mancante o il riferimento a uno stato che non esiste.

Il XAL Designer esegue la validazione backend come parte del processo di commit, prima di inviare le modifiche al repository. Vedere [Commit and Push](../designer/publishing/commit.md) per i dettagli.
