# Riferimento al Passaggio di Parametri

I parametri permettono agli automi di scambiare dati — tra un padre e un figlio al momento della creazione, e tra un automa e le sue funzioni metric o action.

---

## Elemento Parameter

```xml
<Parameter LocalVariable="orderId" TargetVariable="orderId"/>
```

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `LocalVariable` | Sì | Il nome della variabile nel global state dell'automa corrente |
| `TargetVariable` | Sì | Il nome della variabile nel contesto di destinazione (automa figlio o funzione) |
| `Position` | No | Utilizzato in alcuni tipi di funzione per specificare l'indice dell'argomento posizionale |

---

## Parametri di input

I blocchi `<Input>` passano valori **verso** una funzione o un automa figlio.

In una metric o action, i parametri di input forniscono contesto all'implementazione:

```xml
<Action Id="getOrder" Type="object">
  <System Name="getOrderFromShopify" Class="ecommerce.ShopifyConnector" Path="/src/ShopifyConnector.java"/>
  <Input>
    <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="apiEndpoint"/>
    <Parameter LocalVariable="orderId" TargetVariable="orderId"/>
  </Input>
</Action>
```

In un `TransitionNew`, i parametri di input passano valori dal global state del padre al global state del figlio al momento dell'istanziazione:

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

I blocchi `<o>` catturano i valori **restituiti** da una metric o action e li scrivono nel global state dell'automa:

```xml
<Metric Id="checkPickingConfirmation" Type="object">
  <System Name="checkPickingConfirmation" Class="wms.WaterFallConnector" Path="/src/WaterFallConnector.java"/>
  <o>
    <Parameter LocalVariable="confirmedQuantities" TargetVariable="confirmedQuantities"/>
  </o>
</Metric>
```

Dopo l'esecuzione della metric, il valore di `TargetVariable` restituito dalla funzione viene scritto nella `LocalVariable` nel global state dell'automa.

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

Quando il designer scrive i valori dei parametri in XAL, le variabili che corrispondono al nome di una variabile del global state vengono automaticamente precedute dal prefisso `this.GLOBALSTATE_`. Questo prefisso è risolto dal runtime della piattaforma ed è invisibile nell'interfaccia del designer.

Ad esempio, se l'automa ha una variabile del global state chiamata `DbHostName`, un parametro che la referenzia apparirà nel XAL come:

```xml
<Parameter LocalVariable="DbHostName" TargetVariable="DbHostName"/>
```

Ma potrebbe essere memorizzata internamente come `this.GLOBALSTATE_DbHostName` a seconda del contesto.
