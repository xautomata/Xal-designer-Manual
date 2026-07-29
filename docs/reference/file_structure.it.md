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

L'attributo opzionale `parameter` su `<XAL>` specifica il percorso base utilizzato per risolvere i riferimenti relativi ai file negli elementi `TransitionNew`.

---

## Struttura dell'automa

Ogni elemento `<Automaton>` contiene i seguenti elementi figlio, tutti allo stesso livello:

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

## Denominazione e organizzazione dei file

I file XAL usano l'estensione `.xal`. Più automi vengono tipicamente raggruppati in un singolo file quando formano una famiglia — un automa padre e i suoi figli correlati.

I file sono organizzati in una struttura di directory all'interno del repository. Gli elementi `TransitionNew` referenziano gli automi figlio per nome di file e ID automa, usando un percorso relativo alla posizione del file corrente.

---

## Validazione

I file XAL sono validati dalla piattaforma XAUTOMATA al momento del caricamento. Un file che non supera la validazione non viene caricato e i suoi automi non vengono eseguiti.

Il XAL Designer esegue la validazione backend come parte del processo di commit, prima di inviare le modifiche al repository. Vedere [Commit and Push](../designer/publishing/commit.md) per i dettagli.
