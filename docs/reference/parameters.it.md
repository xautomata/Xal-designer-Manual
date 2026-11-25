# Riferimento al Passaggio di Parametri

I parametri permettono agli automi di scambiare dati — tra un padre e un figlio al momento della creazione, e tra un automa e le sue funzioni metric o action.

---

## Elemento Parameter

```xml
<Parameter LocalVariable="orderId" TargetVariable="orderId"/>
```

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `LocalVariable` | Sì | Il nome della variabile nel global state dell'automa corrente, senza prefissi |
| `TargetVariable` | Sì | Il nome della variabile nel contesto di destinazione: la variabile dell'automa figlio, o il parametro del metodo Java |
| `Position` | No | Non usato dall'interprete: gli argomenti di un metodo seguono l'ordine degli elementi `<Parameter>` |

---

## Parametri di input

I blocchi `<Input>` passano valori **verso** una funzione o un automa figlio.

In una metric o action con `Type="object"`, i parametri di input diventano gli argomenti del metodo Java. L'interprete li passa **per posizione**, nell'ordine degli elementi `<Parameter>`: l'ordine deve corrispondere alla firma del metodo. `TargetVariable` riporta il nome del parametro del metodo, per leggibilità. Se la funzione ha una `<Query>` o una `<DataSource>`, i loro valori vengono prima (vedi [Actions](actions.md#query)).

```xml
<Action Id="getOrder" Type="object">
  <System Name="getOrderFromShopify" Class="ecommerce.ShopifyConnector" Path="/src/ShopifyConnector.java"/>
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</Action>
```

In una `TransitionNew`, una `TransitionNewMulti` o una funzione con `Type="automaton"`, i parametri di input copiano valori dal global state del padre al global state del figlio al momento dell'istanziazione. Il valore di `LocalVariable` nel padre diventa il valore iniziale di `TargetVariable` nel figlio:

```xml
<TransitionNew IdInputState="newOrder" IdOutputState="instantiatedOperator"
  Path="ecommerce_backoffice.xal" Type="Operator">
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
    <Parameter LocalVariable="shopifyCredentials" TargetVariable="shopifyCredentials"/>
  </Input>
</TransitionNew>
```

---

## Parametri di output

I blocchi `<Output>` catturano i valori **restituiti** da una metric o action e li scrivono nel global state dell'automa:

```xml
<Metric Id="checkPickingConfirmation" Type="object">
  <System Name="checkPickingConfirmation" Class="wms.WaterFallConnector" Path="/src/WaterFallConnector.java"/>
  <Output>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </Output>
</Metric>
```

Con `Type="object"`:

- con un solo parametro di output, il valore restituito dal metodo viene scritto nella sua `LocalVariable`;
- con più parametri, il metodo restituisce una mappa le cui chiavi sono i nomi delle `LocalVariable`, e ogni valore viene scritto nella variabile corrispondente.

Con `Type="automaton"`, quando il figlio finisce, il valore di ogni `TargetVariable` del figlio viene scritto nella `LocalVariable` corrispondente del padre.

Per il valore che guida le transizioni di una metric, vedi [Metrics](metrics.md#output).

---

## Parametri di input loop

`<LoopInput>` è usato esclusivamente in `TransitionNewMulti`. Contiene un singolo `<Parameter>` che punta a una variabile lista nel global state del padre. La transizione itera sulla lista e crea un'istanza figlio per ogni elemento:

```xml
<TransitionNewMulti IdInputState="Start" IdOutputState="Cycle"
  Path="patcher.xal" Type="RecycleAppPools" MinWait="300">
  <LoopInput>
    <Parameter LocalVariable="AppPools" TargetVariable="AppPool"/>
  </LoopInput>
</TransitionNewMulti>
```

Ogni figlio creato riceve un elemento dalla lista `AppPools` come valore della sua variabile `AppPool`.

---

## Risoluzione delle variabili

In un `<Parameter>`, `LocalVariable` è sempre il nome semplice di una variabile del global state, per esempio `DbHostName`:

```xml
<Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
```

Il prefisso `this.GLOBALSTATE_` si usa altrove, dove l'XAL si aspetta un valore (vedi [Referenziare le variabili](global_state_clocks.md#referenziare-le-variabili)).

---

## Valori di input speciali

Al posto di una variabile del global state, `LocalVariable` può indicare uno di questi valori, preparati dall'interprete:

| `LocalVariable` | Tipo | Valore |
|---|---|---|
| `XAL_EXCEPTION_DESCRIPTION` | `string` | Il dettaglio dell'ultimo errore sollevato da una action o da una metric dell'istanza |
| `XAL_SELF_DESCRIPTION` | `string` | La descrizione dell'istanza corrente |
| `XAL_GLOBAL_STATE_VARS` | `map[string,string]` | Tutte le variabili del global state dell'istanza, con i loro valori come testo |

`TargetVariable` dà il nome che il valore prende nell'automa figlio o nel metodo.
