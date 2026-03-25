# Clocks and Time

Clocks allow an automaton to measure elapsed time and make decisions based on how long it has been in a given state or waiting for a condition to change.

---

## What a clock is

A clock is a timer variable defined at the automaton level. It starts counting when it is reset, and its current value can be read at any time by a transition condition.

Clocks have no unit defined in the automaton itself — the unit is specified when the clock constraint is written. In practice, clock values are compared against named constants such as `T_max_wait` or `T_handshake_timeout`, whose actual values are configured at the platform level.

---

## How clocks are used

Clocks are used in two ways in transitions.

A **clock constraint** is a condition that must be satisfied for the transition to fire. For example:

- `clk_retry >= 5 min` — the transition fires only after 5 minutes have elapsed since the clock was last reset
- `clk_handshake < T_handshake_timeout` — the transition fires only if the handshake clock has not yet exceeded its timeout

A **clock reset** restarts a clock when a transition fires. Resets are used to measure time from a specific event — for example, resetting a retry clock every time a connection attempt fails.

---

## Combining constraints and resets

Clocks become most useful when constraints and resets are combined across multiple transitions.

A typical pattern is:

1. A transition resets the clock when entering a waiting state.
2. One transition fires if the clock is still within an acceptable range — keeping the automaton in the waiting state.
3. Another transition fires when the clock exceeds the timeout — moving the automaton to an error or exception state.

This pattern allows automata to implement retry logic, watchdog timers, and escalation workflows without any external scheduling.

---

## Defining clocks

Clocks are defined at the automaton level, not at the state or transition level. Every clock used in any transition of an automaton must be declared in the automaton's clock list.

In the XAL Designer, clocks are managed from the **Clock Vars** panel in the left sidebar.
