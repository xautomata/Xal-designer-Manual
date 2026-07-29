# Come gli automata si relazionano alla piattaforma

Gli automata non operano in isolamento. Sono profondamente integrati con la piattaforma XAUTOMATA e interagiscono con le sue entità, i dati di monitoraggio e i flussi di lavoro operativi.

---

## Automata e oggetti monitorati

Ogni istanza di automaton è associata a uno o più oggetti monitorati nella piattaforma — server, dispositivi di rete, servizi o qualsiasi altra risorsa infrastrutturale.

L'automaton legge i dati di metric prodotti da quegli oggetti e reagisce ai cambiamenti nel loro stato operativo. Questo è ciò che rende l'automaton un **digital twin** del sistema monitorato: rispecchia il comportamento reale dell'infrastruttura in una forma strutturata e interrogabile.

---

## Automata e dispatcher

Quando un automaton effettua una Transition tra stati, può attivare un **dispatcher**. Un dispatcher è una regola operativa che collega una transizione di stato a un'azione esterna — inviare una notifica, aprire un ticket o chiamare un'API.

I dispatcher possono essere essi stessi implementati come automata. In questo caso, un automaton figlio dedicato monitora lo stato del padre e gestisce tutte le comunicazioni in uscita, mantenendo pulita la logica del processo principale.

---

## Automata e metrics nella piattaforma

Le metrics osservate dagli automata sono le stesse metrics raccolte dalle probe di monitoraggio della piattaforma. Quando una probe rileva un cambiamento in un oggetto infrastrutturale, quel cambiamento fluisce nella metric e l'automaton reagisce di conseguenza.

Questa connessione significa che gli automata rispondono agli eventi infrastrutturali reali in tempo quasi reale, senza richiedere intervento manuale.

---

## Lo XAL Designer nel contesto

Lo XAL Designer è lo strumento usato per creare e modificare gli automata. Si connette a un repository Git contenente file `.xal` e fornisce una canvas visuale per modificare la logica degli automata.

Le modifiche apportate nel designer vengono committate nel repository tramite un workflow Git standard. La piattaforma poi preleva i file aggiornati e li applica alle istanze di automaton in esecuzione.

Il flusso complessivo è:

1. Definire o modificare la logica dell'automaton nello XAL Designer.
2. Committare e fare push delle modifiche nel repository.
3. La piattaforma valida e distribuisce gli automata aggiornati.
4. Le istanze in esecuzione iniziano ad applicare la nuova logica.

!!! note
    La piattaforma XAUTOMATA valida tutti i file XAL al momento del caricamento. Un file che non supera la validazione non verrà caricato, indipendentemente da ciò che mostra il designer.
