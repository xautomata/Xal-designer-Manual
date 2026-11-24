# Query Syntax

Metrics and some actions use a SQL-like query to read data from the platform's runtime database. This page describes the query format and the conventions used throughout XAL files.

---

## Basic structure

A query follows standard SQL SELECT syntax:

```sql
SELECT <column> FROM <table> WHERE groupLabel = 'this.GROUPLABEL'
```

The most common form reads the global state status of another automaton:

```sql
SELECT GLOBALSTATE_STATUS FROM scenario_FollowerManager WHERE groupLabel = 'this.GROUPLABEL'
```

---

## Table naming convention

Each automaton has its own table. The table name joins the XAL file name and the automaton `Id` with an underscore:

```
<FileName>_<AutomatonId>
```

`<FileName>` is the XAL file name without the `.xal` extension, unchanged. `<AutomatonId>` is the `Id` attribute of the target automaton.

Examples:

| File | Automaton | Table name |
|---|---|---|
| `scenario.xal` | `FollowerManager` | `scenario_FollowerManager` |
| `patcher.xal` | `Dispatcher` | `patcher_Dispatcher` |

Upper and lower case do not matter in the table name. `patcher_Dispatcher`, `PATCHER_DISPATCHER` and `Patcher_dispatcher` read the same table.

!!! note
    This applies only to table names in queries. In the `Path` of a TransitionNew, write the file name exactly, with the same case.

!!! warning
    Do not use hyphens in the name of a XAL file that other automata query. The platform cannot create a table for a file like `drone-leader.xal`, so every query on it fails.

---

## The groupLabel binding

The `WHERE groupLabel = 'this.GROUPLABEL'` clause is mandatory in all automaton state queries. It binds the query to the specific instance of the target automaton that is running on the same monitored object as the querying automaton.

Without this binding, the query would return results from all running instances of the target automaton, regardless of which object they belong to.

`this.GROUPLABEL` is a runtime placeholder — the platform substitutes it with the actual group label of the current instance at execution time.

---

## Querying specific columns

In addition to `GLOBALSTATE_STATUS`, queries can retrieve other global state variables:

```sql
SELECT GLOBALSTATE_STATUS as "status", GLOBALSTATE_RelocatedDBs as "RelocatedDBs"
FROM PATCHER_RelocateDb
WHERE groupLabel = 'this.DbHostName'
```

The `as` alias maps the column to the output parameter that receives the value in the automaton's global state.

---

## Alternative WHERE clauses

Some queries use a specific global state variable in the WHERE clause instead of `this.GROUPLABEL`:

```sql
WHERE groupLabel = 'this.DbHostName'
```

This targets the instance associated with the value stored in the `DbHostName` variable of the current automaton's global state.

---

## Queries in the XAL Designer

In the XAL Designer, the query is entered in the **Query** field of the Metric configuration section. The designer does not validate the query syntax — correctness is verified by the platform at runtime.

When a state has a query configured on its metric, a **grid icon** appears in the top-right corner of the state node on the canvas.
