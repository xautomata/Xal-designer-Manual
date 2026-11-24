# Sintassi delle Query

Le metric e alcune action usano una query simile a SQL per leggere dati dal database runtime della piattaforma. Questa pagina descrive il formato della query e le convenzioni usate nei file XAL.

---

## Struttura base

Una query segue la sintassi SQL SELECT standard:

```sql
SELECT <column> FROM <table> WHERE groupLabel = 'this.GROUPLABEL'
```

La forma più comune legge lo stato del global state di un altro automa:

```sql
SELECT GLOBALSTATE_STATUS FROM Scenario_Follower_Manager WHERE groupLabel = 'this.GROUPLABEL'
```

---

## Convenzione di denominazione delle tabelle

Ogni automa ha una sua tabella. Il nome della tabella unisce il nome del file XAL e l'`Id` dell'automa con un underscore:

```
<FileName>_<AutomatonId>
```

`<FileName>` è il nome del file XAL senza l'estensione `.xal`, invariato. `<AutomatonId>` è l'attributo `Id` dell'automa di destinazione.

Esempi:

| File | Automa | Nome tabella |
|---|---|---|
| `scenario.xal` | `Follower_Manager` | `scenario_Follower_Manager` |
| `patcher.xal` | `Dispatcher` | `patcher_Dispatcher` |

Maiuscole e minuscole non contano nel nome della tabella. `patcher_Dispatcher`, `PATCHER_DISPATCHER` e `Patcher_dispatcher` leggono la stessa tabella.

!!! note
    Questo vale solo per i nomi delle tabelle nelle query. Nel `Path` di una TransitionNew scrivi il nome del file esatto, con le stesse maiuscole.

!!! warning
    Non usare trattini nel nome di un file XAL che altri automi interrogano. La piattaforma non riesce a creare la tabella di un file come `drone-leader.xal`, quindi ogni query su quella tabella fallisce.

---

## Il binding groupLabel

La clausola `WHERE groupLabel = 'this.GROUPLABEL'` è obbligatoria in tutte le query sullo stato degli automi. Lega la query alla specifica istanza dell'automa di destinazione che è in esecuzione sullo stesso oggetto monitorato dell'automa richiedente.

Senza questo binding, la query restituirebbe risultati da tutte le istanze in esecuzione dell'automa di destinazione, indipendentemente dall'oggetto a cui appartengono.

`this.GROUPLABEL` è un segnaposto runtime — la piattaforma lo sostituisce con l'effettiva etichetta di gruppo dell'istanza corrente al momento dell'esecuzione.

---

## Query su colonne specifiche

Oltre a `GLOBALSTATE_STATUS`, le query possono recuperare altre variabili del global state:

```sql
SELECT GLOBALSTATE_STATUS as "status", GLOBALSTATE_RelocatedDBs as "RelocatedDBs"
FROM PATCHER_RelocateDb
WHERE groupLabel = 'this.DbHostName'
```

L'alias `as` mappa la colonna sul parametro di output che riceve il valore nel global state dell'automa.

---

## Clausole WHERE alternative

Alcune query usano una variabile specifica del global state nella clausola WHERE invece di `this.GROUPLABEL`:

```sql
WHERE groupLabel = 'this.DbHostName'
```

Questo punta all'istanza associata al valore memorizzato nella variabile `DbHostName` del global state dell'automa corrente.

---

## Query nel XAL Designer

Nel XAL Designer, la query viene inserita nel campo **Query** della sezione di configurazione della Metric. Il designer non valida la sintassi della query — la correttezza è verificata dalla piattaforma a runtime.

Quando uno stato ha una query configurata sulla sua metric, nella parte in alto a destra del nodo dello stato sul canvas appare un'**icona griglia**.
