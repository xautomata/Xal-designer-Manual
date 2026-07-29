# Riferimento Automaton

L'elemento `<Automaton>` è il contenitore di primo livello per la definizione di un singolo automa all'interno di un file XAL.

---

## Attributi

| Attributo | Obbligatorio | Descrizione |
|---|---|---|
| `Id` | Sì | Identificatore univoco dell'automa all'interno del file. Utilizzato dagli elementi `TransitionNew` e dalle query delle metric per referenziare questo automa. |

---

## Elementi figlio

| Elemento | Obbligatorio | Descrizione |
|---|---|---|
| `<Functions>` | Sì | Contiene le definizioni di `<Action>` e `<Metric>`. Deve essere presente anche se vuoto. |
| `<States>` | Sì | Contiene tutte le definizioni di `<State>`. |
| `<InitialState>` | Sì | Dichiara lo stato iniziale tramite `IdState`. |
| `<Transitions>` | Sì | Contiene tutti gli elementi di transizione. |
| `<GlobalState>` | No | Contiene le definizioni di `<Variable>` per lo stato condiviso. |
| `<Clocks>` | No | Contiene le definizioni di `<Variable>` per i timer degli orologi. |
| `<FinalStates>` | No | Contiene gli elementi `<FinalState>` che dichiarano gli stati terminali. |

---

## Esempio minimale

Un automa con due stati e una transizione:

```xml
<Automaton Id="SimpleMonitor">
  <Functions/>
  <GlobalState>
    <Variable Name="status" Type="string"/>
  </GlobalState>
  <Clocks/>
  <States>
    <State Id="Watching"/>
    <State Id="Alerted"/>
  </States>
  <InitialState IdState="Watching"/>
  <FinalStates>
    <FinalState IdState="Alerted"/>
  </FinalStates>
  <Transitions>
    <Transition
      IdInputState="Watching"
      IdOutputState="Alerted"
      MetricValue="critical"
      IgnoreMinWait="true"/>
  </Transitions>
</Automaton>
```

---

## Più automi in un unico file

Un singolo file XAL contiene comunemente una famiglia di automi correlati. L'automa padre referenzia i figli tramite il loro `Id` negli elementi `TransitionNew`, e i figli referenziano il padre attraverso le query delle metric.

```xml
<XAL>
  <Automaton Id="Parent">
    ...
    <Transitions>
      <TransitionNew
        IdInputState="Init"
        IdOutputState="Running"
        Path="myfile.xal"
        Type="Child"
        MinWait="300"/>
    </Transitions>
  </Automaton>

  <Automaton Id="Child">
    ...
  </Automaton>
</XAL>
```

Tutti gli ID degli automi all'interno dello stesso file devono essere univoci.
