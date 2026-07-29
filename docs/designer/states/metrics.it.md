# Configurare le Metric degli Stati

Una Metric associata a uno stato definisce cosa l'automa osserva mentre si trova in quello stato. I valori restituiti dalla Metric determinano quale transizione in uscita scatta successivamente.

Per configurare una Metric, seleziona uno stato nel canvas ed espandi la sezione **Metric** nel pannello destro.

![Pannello configurazione Metric](../../images/designer/states/metric_panel.png)
/// caption
Fig.1 - La sezione Metric del pannello di configurazione di uno stato
///

---

## Valori di enumerazione

L'enumerazione definisce l'insieme dei valori che la Metric può restituire. Ogni valore corrisponde a un possibile risultato con cui una transizione in uscita può confrontarsi.

Per aggiungere un valore, digitalo nel campo **New Symbol** e fai clic su **+**. Per rimuovere un valore, fai clic sul pulsante **×** accanto ad esso.

!!! info
    I valori di enumerazione vengono usati come condizioni **Metric Value** sulle transizioni in uscita. Se il valore Metric Value di una transizione non corrisponde ad alcun valore nell'enumerazione, non scatterà mai.

---

## Query

Il campo **Query** contiene l'istruzione SQL-like usata per leggere il valore della Metric a runtime. Tipicamente legge lo stato globale di un altro automa o di un oggetto monitorato.

Una query segue questo schema:

```sql
SELECT GLOBALSTATE_STATUS FROM <File>_<Automaton> WHERE groupLabel = 'this.GROUPLABEL'
```

Il binding `this.GROUPLABEL` assicura che la query faccia riferimento all'istanza corretta dell'automa — quella in esecuzione sullo stesso oggetto monitorato.

Quando è configurata una query, il nodo stato nel canvas mostra un'**icona griglia** nell'angolo in alto a destra.

---

## Configurazione Metric Data

La sezione **Metric Data** specifica come viene eseguita la Metric.

| Campo | Descrizione |
|---|---|
| **Type** | Il contesto di esecuzione: `Object` per le Metric legate agli oggetti di infrastruttura monitorati, `Automaton` per le Metric che fanno riferimento direttamente ad altre istanze di automa |
| **System file** | Il path al file di implementazione (es. `/src/Utils.java`) usato dalla piattaforma per risolvere la Metric a runtime |

Se il file di sistema non viene trovato nel repository corrente, il designer mostra un avviso. Questo non impedisce all'automa di funzionare — il file potrebbe esistere nell'ambiente di runtime della piattaforma.

---

## Azzerare la Metric

Fai clic su **Reset** per cancellare l'intera configurazione Metric dallo stato.

!!! warning
    Azzerare una Metric rimuove la query, tutti i valori di enumerazione e il riferimento al file di sistema. Le transizioni che si basano sui valori Metric di questo stato non avranno più condizioni corrispondenti.
