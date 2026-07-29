# Variabili di Stato Globale

Le variabili di stato globale memorizzano dati che persistono per l'intero ciclo di vita di un'istanza di automa. Fungono da memoria dell'automa — conservano valori che stati e transizioni possono leggere e scrivere.

Per visualizzare e gestire le variabili di stato globale, fai clic sull'**icona globo** nella barra laterale sinistra. Il pannello mostra tutte le variabili definite per l'automa attualmente attivo.

![Pannello Global State](../../images/designer/automata/global_state_panel.png)
/// caption
Fig.1 - Il pannello Global State Variables
///

---

## Aggiungere una variabile

Fai clic su **+** o sul pulsante di aggiunta per creare una nuova variabile. Ogni variabile ha:

| Campo | Descrizione |
|---|---|
| **Name** | L'identificatore usato per fare riferimento alla variabile in tutto l'automa |
| **Type** | Il tipo di dato (es. `string`, `int`, `boolean`, `list`) |
| **Value** | Un valore predefinito opzionale assegnato quando l'automa viene istanziato per la prima volta |

---

## Modificare una variabile

Fai clic su una scheda variabile per modificarla inline. Le modifiche vengono applicate immediatamente all'automa.

Per eliminare una variabile, fai clic sull'**icona cestino** sulla scheda della variabile.

---

## Come vengono usate le variabili

Le variabili di stato globale sono referenziate in:

- **Parametri di input e output delle Metric** — per passare valori a una funzione Metric o acquisirne i risultati
- **Parametri di input e output delle Action** — per fornire contesto a un'Action o memorizzarne l'output
- **Parametri di input delle TransitionNew** — per passare dati a un automa figlio nel momento in cui viene istanziato
- **Vincoli Clock** — quando una variabile memorizza un valore soglia usato in una condizione temporale

!!! info
    Le variabili il cui nome corrisponde a un'opzione di stato globale nell'automa vengono automaticamente prefissate con `this.GLOBALSTATE_` nell'output XAL. Questo prefisso è gestito in modo trasparente dal designer — non è mai necessario digitarlo manualmente.
