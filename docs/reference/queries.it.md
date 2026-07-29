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

Il nome della tabella codifica il file e l'automa oggetto della query, usando un separatore underscore:

```
<FilePrefix>_<AutomatonId>
```

Dove `<FilePrefix>` è derivato dal nome del file XAL (senza estensione, con i trattini sostituiti da underscore) e `<AutomatonId>` è l'attributo `Id` dell'automa di destinazione.

Esempi:

| File | Automa | Nome tabella |
|---|---|---|
| `scenario.xal` | `Follower_Manager` | `Scenario_Follower_Manager` |
| `patcher.xal` | `Dispatcher` | `PATCHER_DISPATCHER` |
| `drone-leader.xal` | `C2_Link_Monitor` | `Scenario_C2_Link_Monitor` |

!!! note
    La convenzione esatta di capitalizzazione può variare a seconda del deployment. Seguire le convenzioni usate nei file esistenti all'interno dello stesso progetto.

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
