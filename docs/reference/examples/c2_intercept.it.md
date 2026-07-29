# Esempio Annotato — C2 Intercept

Questo esempio illustra una **gerarchia profonda di automi** con uso intensivo di clock, logica di allocazione risorse e un flusso di lavoro di missione a più fasi.

Il file `c2_intercept.xal` modella uno scenario di intercettazione comando e controllo, dove una missione procede attraverso fasi di valutazione, autorizzazione, esecuzione e analisi — ciascuna delegata a un automa figlio specializzato.

!!! note
    Questo file è uno scenario sintetico progettato per dimostrare le capacità avanzate di XAL. La terminologia specifica del dominio (threat, engagement, intercept) è usata per illustrare pattern operativi complessi e non riflette alcun deployment reale specifico.

---

## Automi in questo file

| Automa | Ruolo |
|---|---|
| `Scenario` | Orchestratore di primo livello — sequenzia le fasi della missione |
| `Threat_Assessor` | Traccia e classifica l'obiettivo |
| `Resource_Allocator` | Cerca le risorse disponibili |
| `Engagement_Manager` | Gestisce l'autorizzazione e monitora la fase attiva |
| `Radio_Link` | Monitora la qualità del collegamento di comunicazione |
| `Mission_Evaluator` | Analizza il risultato dopo l'esecuzione |
| `Dispatcher` | Gestisce le operazioni di dispatch |

---

## Scenario

L'automa `Scenario` sequenzia la missione attraverso cinque stati, creando un figlio specializzato ad ogni transizione di fase:

```
Init_intercept
  → (spawn Threat_Assessor, Dispatcher)
Assessing_threat
  → Threat-classified → (spawn Engagement_Manager)
Allocating_resources
  → Resources-assigned → (spawn Radio_Link)
Awaiting_authorization
  → Engagement-authorized → (spawn Mission_Evaluator)
Executing_intercept
  → Intercept-terminated
Evaluating_outcome
  → Evaluation-complete → Mission_complete / Allocating_resources
```

Ogni `TransitionNew` crea un figlio e fa avanzare il padre. Il padre osserva poi lo stato del figlio tramite una metric fino a quando non viene riportato un valore terminale.

---

## Threat_Assessor

Il `Threat_Assessor` usa un clock per imporre un periodo minimo di tracciamento prima che la classificazione sia consentita:

```xml
<Transition IdInputState="Init_assessment" IdOutputState="Tracking" IgnoreMinWait="true">
  <ClockReset ClockVar="clk_tracking"/>
</Transition>
<Transition IdInputState="Tracking" IdOutputState="Tracking" IgnoreMinWait="true">
  <ClockConstraint ClockExp="clk_tracking &lt; T_stable_contact"/>
</Transition>
<Transition IdInputState="Tracking" IdOutputState="Classifying">
  <ClockConstraint ClockExp="clk_tracking &gt;= T_stable_contact"/>
</Transition>
```

L'automa rimane in loop in `Tracking` finché il clock supera la soglia di stabilità, poi procede alla classificazione. Questo previene decisioni premature basate su contatti transitori.

---

## Resource_Allocator con logica di retry

Il `Scenario` gestisce la mancanza di risorse usando un self-loop protetto da clock su `Allocating_resources`:

```xml
<Transition IdInputState="Allocating_resources" IdOutputState="Allocating_resources"
  MetricValue="No-resources-available" IgnoreMinWait="true">
  <ClockConstraint ClockExp="t_wait_resources &lt; T_max_wait"/>
</Transition>
<Transition IdInputState="Allocating_resources" IdOutputState="Exception"
  MetricValue="Exception">
  <ClockConstraint ClockExp="t_wait_resources &gt;= T_max_wait"/>
</Transition>
```

Se le risorse non sono disponibili, lo Scenario continua a riprovare finché non viene superato il tempo massimo di attesa, a quel punto transita verso `Exception`. Questo implementa un retry limitato con escalation al timeout.

---

## Engagement_Manager e Radio_Link

`Engagement_Manager` monitora la qualità del collegamento radio tramite l'automa figlio `Radio_Link`. Quando il collegamento viene perso, transita verso uno stato di recupero:

```xml
<Transition IdInputState="Monitoring_approach" IdOutputState="Link_lost_management"
  MetricValue="Link-lost" IgnoreMinWait="true"/>
<Transition IdInputState="Link_lost_management" IdOutputState="Monitoring_approach"
  MetricValue="Link-active" IgnoreMinWait="true"/>
<Transition IdInputState="Link_lost_management" IdOutputState="Intercept_terminated">
  <ClockConstraint ClockExp="clk_link_recovery &gt;= T_link_timeout"/>
</Transition>
```

Se il collegamento viene ripristinato, la missione riprende dal punto in cui era stata interrotta. Se il collegamento rimane assente oltre il timeout, l'intercettazione viene dichiarata terminata in ogni caso.

L'automa `Radio_Link` stesso modella la degradazione del segnale in tre fasi: `Link_active` → `Link_degraded` → `Link_lost`, ciascuna governata da clock separati:

```xml
<Transition IdInputState="Link_active" IdOutputState="Link_degraded">
  <ClockConstraint ClockExp="clk_last_rx &gt;= T_rx_timeout_short"/>
  <ClockReset ClockVar="clk_degraded"/>
</Transition>
<Transition IdInputState="Link_degraded" IdOutputState="Link_lost">
  <ClockConstraint ClockExp="clk_degraded &gt;= T_rx_timeout_long"/>
  <ClockReset ClockVar="clk_lost"/>
</Transition>
```

---

## Mission_Evaluator con esito indeterminato

Il `Mission_Evaluator` gestisce i casi in cui il risultato non può essere determinato immediatamente:

```xml
<Transition IdInputState="Analyzing_outcome" IdOutputState="Outcome_indeterminate"
  IgnoreMinWait="true">
  <ClockReset ClockVar="clk_indeterminate"/>
</Transition>
<Transition IdInputState="Outcome_indeterminate" IdOutputState="Evaluation_complete">
  <ClockConstraint ClockExp="clk_indeterminate &gt;= T_indeterminate_timeout"/>
</Transition>
```

Se il risultato rimane indeterminato oltre il timeout, la valutazione viene forzata a completarsi. Questo previene il blocco indefinito della missione su uno stato irrisolto.

---

## Pattern chiave illustrati

- **Orchestrazione sequenziale delle fasi**: ogni fase creata come figlio, il padre avanza al completamento
- **Periodo minimo di osservazione**: tempo di permanenza imposto dal clock prima che le transizioni di stato siano consentite
- **Retry limitato con timeout**: self-loop con condizione di clock, escalation all'eccezione al timeout
- **Degradazione controllata**: perdita del collegamento gestita con logica di recupero e timeout di fallback
- **Modellazione del segnale a più stadi**: stati di degradazione progressiva con clock indipendenti
- **Risoluzione forzata**: stati indeterminati risolti per timeout per prevenire il blocco
