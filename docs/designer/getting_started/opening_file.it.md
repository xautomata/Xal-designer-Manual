# Aprire un File

Quando si apre il XAL Designer, una finestra di dialogo di benvenuto consente di scegliere come caricare i file XAL.

![Finestra di dialogo di benvenuto](../../images/designer/getting_started/welcome.png)
/// caption
Fig.1 - La finestra di dialogo di benvenuto
///

---

## Connettersi a un repository

Seleziona uno dei provider di controllo versione disponibili: **GitHub**, **GitLab** o **Bitbucket**.

Dopo l'autenticazione, utilizza i campi **Repository** e **Branch** nella barra superiore per selezionare il repository e il branch su cui vuoi lavorare. Il designer carica automaticamente la struttura dei file.

Una volta collegato, il **Repository Explorer** nella barra laterale sinistra mostra la struttura di cartelle e file del branch selezionato. I file XAL sono elencati nelle rispettive directory e possono essere espansi per mostrare gli automi che contengono.

Per aprire un automa, fai clic sul suo nome nel Repository Explorer. Si apre come nuovo tab nel canvas.

![Repository Explorer](../../images/designer/getting_started/repo_explorer.png)
/// caption
Fig.2 - Il Repository Explorer con un repository caricato
///

---

## Lavorare nel browser

Seleziona **Start in browser** per lavorare senza connettersi a un sistema di controllo versione. Usa il pulsante di caricamento nella barra superiore per caricare un file `.xal` locale dal tuo computer.

!!! warning
    Le modifiche effettuate in modalità browser non possono essere committate su un repository. Assicurati di esportare o salvare il tuo lavoro manualmente prima di chiudere la sessione.

!!! note
    Un problema noto riguarda il pulsante di caricamento in modalità browser. Vedi `qa.md` — Q7 per i dettagli.

---

## Cambiare repository

Per passare a un repository o branch diverso, aggiorna i campi **Repository** e **Branch** nella barra superiore in qualsiasi momento. Il designer ricarica di conseguenza la struttura dei file.
