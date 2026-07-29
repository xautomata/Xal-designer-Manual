# Metric Value e Clock

I campi **Metric Value** e **Clocks** nel pannello della transizione controllano quando una transizione scatta. Possono essere usati in modo indipendente o in combinazione.

---

## Metric Value

Il campo **Metric Value** specifica il valore che la Metric dello stato di input deve restituire affinché la transizione scatti.

Seleziona un valore dal menu a discesa. Le opzioni disponibili sono tratte dall'enumerazione definita sulla Metric associata allo stato di input. Se lo stato di input non ha una Metric, o la sua Metric non ha valori di enumerazione, il menu a discesa sarà vuoto.

Se non è impostato alcun Metric Value, la transizione scatta indipendentemente dal valore corrente della Metric — purché siano soddisfatti anche gli eventuali vincoli Clock.

!!! warning
    Solo una transizione dallo stesso stato di input può avere la stessa combinazione di Metric Value e Clock Constraint. Se tenti di configurare un duplicato, il pannello mostrerà un errore **"Metric value already in use"**.

---

## Clock Constraint

La sezione **Clocks** consente di aggiungere condizioni basate sul tempo alla transizione.

Per aggiungere un vincolo, seleziona una **Clock Variable**, un **Operator** (`<`, `<=`, `>`, `>=`), un **Value** e un'**Unit**. Il vincolo viene visualizzato come espressione del tipo `clk_retry >= 5 min`.

Una transizione può avere più vincoli Clock. Tutti i vincoli devono essere soddisfatti simultaneamente affinché la transizione scatti.

Per rimuovere un vincolo, fai clic sull'icona **×** accanto ad esso.

---

## Clock Reset Variables

Usa il campo **Clock Reset Variables** per selezionare uno o più Clock da azzerare quando la transizione scatta.

Fai clic sul menu a discesa per vedere tutti i Clock definiti per l'automa corrente e seleziona quelli da azzerare. Ogni Clock selezionato viene mostrato come tag che può essere rimosso singolarmente.

---

## Ignore Min Wait

Il toggle **Ignore Min Wait** bypassa l'intervallo minimo di attesa applicato tra successive attivazioni della stessa transizione. Abilitalo quando la transizione deve reagire immediatamente a un cambiamento, senza alcun ritardo.

---

## Combinare le condizioni

Metric Value e Clock Constraint possono essere usati insieme sulla stessa transizione. In quel caso, entrambe le condizioni devono essere vere simultaneamente affinché la transizione scatti — la Metric deve aver restituito il valore specificato e tutti i vincoli Clock devono essere soddisfatti.

Questa combinazione è comunemente usata per implementare l'escalation per timeout: una transizione gestisce il caso normale (il valore Metric corrisponde, il Clock è nei limiti) e un'altra gestisce il caso di timeout (stesso valore Metric, Clock superato).
