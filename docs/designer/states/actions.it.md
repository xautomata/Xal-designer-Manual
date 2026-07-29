# Configurare le Action degli Stati

Un'Action associata a uno stato definisce un'operazione che l'automa esegue quando entra in quello stato. Le Action interagiscono con sistemi esterni — API, piattaforme ERP, sistemi di gestione magazzino o qualsiasi altro target di integrazione.

Per configurare un'Action, seleziona uno stato nel canvas ed espandi la sezione **Action** nel pannello destro.

---

## Query

Il campo **Query** è un'espressione SQL-like opzionale usata da alcuni tipi di Action per recuperare il contesto prima dell'esecuzione. Non tutte le Action richiedono una query — lascia questo campo vuoto se l'Action opera esclusivamente tramite il suo file di sistema e i parametri di input.

---

## Configurazione Action Data

La sezione **Action Data** specifica come viene eseguita l'Action.

| Campo | Descrizione |
|---|---|
| **Type** | Il contesto di esecuzione: `Object` per le Action legate agli oggetti di infrastruttura monitorati, `Automaton` per le Action che interagiscono direttamente con altre istanze di automa |
| **System file** | Il path al file di implementazione che la piattaforma usa per eseguire l'Action a runtime |

Se il file di sistema non viene trovato nel repository corrente, il designer mostra un avviso. Il file potrebbe esistere nell'ambiente di runtime della piattaforma e l'automa funzionerà comunque correttamente.

!!! info
    Le classi e i file di sistema disponibili per l'uso nelle Action dipendono dal deployment della piattaforma. Per un elenco delle implementazioni standard, contatta il team di delivery di XAUTOMATA.

---

## L'indicatore di avviso

Se la sezione Action è presente ma non ancora configurata, l'intestazione della sezione mostra un indicatore **⚠**. È un promemoria visivo che lo stato ha uno slot Action non ancora compilato. Non impedisce all'automa di essere salvato o committato.
