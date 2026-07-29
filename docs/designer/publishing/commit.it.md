# Commit e Push

Quando hai terminato di modificare un automa, puoi pubblicare le modifiche nel repository collegato utilizzando la funzione **Commit & Push**.

---

## L'indicatore di modifiche non salvate

Durante la modifica, un **indicatore a punto** appare sul tab di qualsiasi automa che ha modifiche non ancora committate. Questo include modifiche strutturali (aggiunta o rimozione di stati e transizioni), modifiche di configurazione e modifiche di layout come lo spostamento di nodi nel canvas.

![Indicatore modifiche non committate](../../images/designer/publishing/dirty_tab.png)
/// caption
Fig.1 - L'indicatore a punto segnala modifiche non committate su un tab
///

---

## Aprire la finestra di dialogo Commit

Fai clic sul **pulsante upload/commit** nella barra superiore per aprire la finestra di dialogo **Commit & Push**.

![Finestra di dialogo Commit](../../images/designer/publishing/commit_dialog.png)
/// caption
Fig.2 - La finestra di dialogo Commit & Push
///

La finestra di dialogo mostra:

| Sezione | Descrizione |
|---|---|
| **Changes included** | Un elenco di tutti i file modificati. Ogni file ha un toggle per includerlo o escluderlo dal commit. Usa **Deselect all** per deselezionare tutti i file contemporaneamente. |
| **Commit message** | Un campo di testo per il messaggio di commit, pre-compilato con `Commit from XAL Designer`. Modificalo per descrivere le tue modifiche. |
| **Validation results** | Il risultato della validazione backend eseguita su tutti i file inclusi. Un segno di spunta verde e **0 files with errors** significa che tutti i file sono validi e pronti per il commit. |

---

## Validazione prima del commit

Prima che il commit venga inviato, la piattaforma valida tutti i file XAL inclusi rispetto allo schema e alle regole di runtime. Questa è la validazione autorevole — è lo stesso controllo che viene eseguito quando la piattaforma carica un automa.

Se un file contiene errori, la sezione dei risultati di validazione mostra il numero di file con errori. Risolvi i problemi prima di fare il commit.

!!! note
    Il designer può mostrare avvisi di validazione del modulo (come messaggi sul formato dei campi) che sono indipendenti dalla validazione backend. Un file che supera la validazione backend è considerato corretto, anche se il designer mostra avvisi minori.

---

## Completare il commit

Una volta superata la validazione, fai clic su **Commit & Push** per pubblicare le modifiche. Le modifiche vengono inviate al branch selezionato del repository collegato.

L'indicatore a punto scompare dai tab interessati dopo un commit riuscito.

---

## Chiudere con modifiche non salvate

Se tenti di chiudere un tab che ha modifiche non committate, il designer mostra una finestra di dialogo di conferma:

> **Uncommitted changes — Are you sure you want to close?**

Fai clic su **Confirm** per scartare le modifiche e chiudere il tab. Fai clic su **Cancel** per tornare all'editor.

!!! warning
    Chiudere un tab con modifiche non committate elimina definitivamente tali modifiche. Non è possibile recuperarle dall'interno del designer.
