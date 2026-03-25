# Annotated Example — E-commerce Backoffice

This example illustrates the **Starter / Operator** pattern: a persistent automaton that monitors an external system for events, and spawns a dedicated child automaton to handle each one.

The file `ecommerce_backoffice.xal` models the backend processing of e-commerce orders, integrating a Shopify storefront with an ERP and a warehouse management system (WMS).

---

## Automata in this file

| Automaton | Role |
|---|---|
| `Starter` | Polls Shopify for new orders and spawns one `Operator` per order |
| `Operator` | Manages the full lifecycle of a single order |
| `SyncInvoiceAndDDTHeader_PickingList` | Sub-process: synchronizes billing data and sends a picking list to the WMS |

---

## Starter

The `Starter` automaton runs continuously. It has no final state other than `exception`, meaning it is designed to run indefinitely.

```xml
<Automaton Id="Starter">
  <GlobalState>
    <Variable Name="shopifyApiEndpoint" Type="string"/>
    <Variable Name="shopifyCredentials" Type="string"/>
    <Variable Name="lastProcessedOrders" Type="list[string]" Value="[]"/>
    <Variable Name="communicationErrorCount" Type="int" Value="0"/>
  </GlobalState>
```

The global state stores the API credentials and a list of already-processed order IDs, which the metric uses to avoid processing the same order twice.

```xml
  <Transitions>
    <Transition IdInputState="init" IdOutputState="newOrder" IgnoreMinWait="true"/>

    <Transition IdInputState="newOrder" IdOutputState="newOrder"
      MetricValue="noNewOrders" IgnoreMinWait="true"/>

    <TransitionNew IdInputState="newOrder" IdOutputState="instantiatedOperator"
      IgnoreMinWait="true" Path="ecommerce_backoffice.xal" Type="Operator">
      <Input>
        <Parameter LocalVariable="shopifyApiEndpoint" TargetVariable="shopifyApiEndpoint"/>
        <Parameter LocalVariable="shopifyCredentials" TargetVariable="shopifyCredentials"/>
      </Input>
    </TransitionNew>
```

The key transitions are:

- `newOrder → newOrder` (MetricValue `noNewOrders`): the automaton loops back on itself while no new orders are detected
- `newOrder → instantiatedOperator` (TransitionNew): when a new order is found, an `Operator` is spawned and receives the Shopify credentials
- `instantiatedOperator → newOrder`: immediately after spawning, the Starter returns to watching for the next order

This creates an event loop: one Operator per order, running in parallel, while the Starter keeps watching.

---

## Operator

The `Operator` automaton manages a single order from receipt to invoice. Each state corresponds to one step in the fulfillment workflow.

```xml
<States>
  <State Id="getEcommOrder"     IdAction="getEcommOrderAction"/>
  <State Id="putErpOrder"       IdAction="putErpOrderAction"/>
  <State Id="waitingWMSforPicking" IdMetric="checkWMSPickingConfirmation"/>
  <State Id="putErpDeliveryNote"   IdAction="putErpDeliveryNoteAction"/>
  <State Id="askForShippingApproval" IdMetric="checkShippingApproval"/>
  <State Id="runWMSShipping"    IdAction="runWMSShippingAction"/>
  <State Id="waitingWMSforShipped" IdMetric="checkWMSShipped"/>
  <State Id="runErpDDT"         IdAction="runErpDDTAction"/>
  <State Id="runErpInvoice"     IdAction="runErpInvoiceAction"/>
  <State Id="completed"/>
  <State Id="exception"         IdMetric="handleException"/>
  <State Id="manualIntervention"/>
</States>
```

The flow is linear:

1. Fetch order from Shopify
2. Create order in ERP → spawn `SyncInvoiceAndDDTHeader_PickingList` to sync headers and send picking list to WMS
3. Wait for WMS picking confirmation
4. Create pre-delivery note in ERP
5. Wait for operator shipping approval
6. Authorize WMS to ship
7. Wait for WMS shipping confirmation
8. Generate DDT document
9. Generate invoice → `completed`

Any step that fails transitions to `exception`. The `handleException` metric decides whether to retry or escalate to `manualIntervention`.

---

## SyncInvoiceAndDDTHeader_PickingList

This child automaton handles the synchronization sub-process triggered after the ERP order is created. It runs four actions in sequence:

```
syncInvoiceHeader → syncDDTHeader → pickingListFromERP → pickingListToWMS → completed
```

It is spawned by the Operator via TransitionNew, receives all required credentials and order data as input parameters, and reports its completion through its final state.

---

## Key patterns illustrated

- **Starter / Operator**: a long-running watcher spawning short-lived handlers
- **Linear action pipeline**: states associated only with actions, no metric observation needed between steps
- **Waiting states**: states associated with metrics that poll an external system until a condition is met (`pending` loops back, `confirmed` advances)
- **Exception handling with retry**: a dedicated `exception` state with a metric that decides whether to restart from the beginning or request manual intervention
- **Parameter passing at spawn**: credentials and context passed from Starter to Operator, and from Operator to sub-process
