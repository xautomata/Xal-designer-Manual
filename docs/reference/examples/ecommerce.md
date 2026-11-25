# Annotated Example — E-commerce Backoffice

This example illustrates the **Starter / Operator** pattern: a persistent automaton that monitors an external system for events, and spawns a dedicated child automaton to handle each one.

The file `ecommerce_backoffice.xal` models the backend processing of e-commerce orders, integrating a Shopify storefront with an ERP and a warehouse management system (WMS).

The snippets below come from that file, cut down to the attributes that matter for each point.

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

    <Transition IdInputState="newOrder" IdOutputState="instantiatedOperator"
      MetricValue="newOrderFound" IgnoreMinWait="true"/>
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
- `newOrder → instantiatedOperator` (Transition + TransitionNew): when a new order is found, an `Operator` is spawned and receives the Shopify endpoint and credentials
- `instantiatedOperator → newOrder`: immediately after spawning, the Starter returns to watching for the next order

A TransitionNew has no `MetricValue`, so on its own it would be valid whatever the metric says, together with the `noNewOrders` loop. The Transition between the same two states gives it the `newOrderFound` condition: the interpreter merges the two into one transition. See [TransitionNew](../transitions.md#transitionnew).

This creates an event loop: one Operator per order, running in parallel, while the Starter keeps watching.

### Retry with a timeout

When Shopify cannot be reached, the Starter moves to `communicationError` and resets two clocks. It retries every 5 minutes; when the metric reports `timeout`, it gives up, but not before 30 minutes:

```xml
<Transition IdInputState="communicationError" IdOutputState="communicationError"
  MetricValue="connectionError">
  <ClockConstraint ClockExp="retryTimer &gt;= 5 min"/>
  <ClockReset ClockVar="retryTimer"/>
</Transition>
<Transition IdInputState="communicationError" IdOutputState="newOrder"
  IgnoreMinWait="true" MetricValue="connected"/>
<Transition IdInputState="communicationError" IdOutputState="exception"
  MetricValue="timeout">
  <ClockConstraint ClockExp="communicationErrorTimer &gt;= 30 min"/>
</Transition>
```

`retryTimer` is reset at each retry, so it measures the time since the last attempt; `communicationErrorTimer` is reset only on entering the state, so it measures the whole outage.

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

The waiting states go to `exception` when their metric reports a problem (`error`, or `rejected` for the shipping approval). There, the `handleException` metric decides whether to start again from `getEcommOrder` after 5 minutes or escalate to `manualIntervention`.

---

## SyncInvoiceAndDDTHeader_PickingList

This child automaton handles the synchronization sub-process triggered after the ERP order is created. It runs four actions in sequence:

```
syncInvoiceHeader → syncDDTHeader → pickingListFromERP → pickingListToWMS → completed
```

It is spawned by the Operator via TransitionNew and receives the ERP and WMS credentials and the order data as input parameters. The Operator does not wait for it: it moves on to `waitingWMSforPicking`, where it waits for the effect of the child on the WMS (the picking confirmation) rather than for the child itself.

---

## Key patterns illustrated

- **Starter / Operator**: a long-running watcher spawning short-lived handlers
- **Linear action pipeline**: states associated only with actions, no metric observation needed between steps
- **Waiting states**: states associated with metrics that poll an external system until a condition is met (`pending` loops back, `confirmed` advances)
- **Retry with a timeout**: two clocks, one for the pace of the retries and one for the total time allowed
- **Exception handling with retry**: a dedicated `exception` state with a metric that decides whether to restart from the beginning or request manual intervention
- **Parameter passing at spawn**: credentials and context passed from Starter to Operator, and from Operator to sub-process
