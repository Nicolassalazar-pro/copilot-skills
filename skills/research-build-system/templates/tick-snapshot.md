# Tick #{{N}} — {{short title}}

<!--
The per-experiment writeup. Companion to the machine-readable tick{{N}}.json
(exact numbers) and tick{{N}}.log (raw run). This .md is the human narrative.
Keep it honest: document failures and surprises as carefully as wins.
-->

## Question

{{What are you testing this tick, and why is it the highest-leverage move right
now? What decision will the result drive?}}

## Method

- {{exact setup: data window, parameters, seeds, constraints applied}}
- {{what makes this a fair test — note any frozen inputs / no-leakage measures}}
- Decision rule decided IN ADVANCE: {{e.g. "if metric >= X across N seeds → promote; else hold"}}

## Result

| variant | metric | {{secondary}} | seeds positive | notes |
|---------|--------|---------------|----------------|-------|
| {{...}} | {{...}} | {{...}}       | {{...}}        | {{...}} |

{{The honest headline number. If it disappointed, say so plainly.}}

## What this proves / kills

- {{the durable takeaway — what is now confirmed, what is now ruled out}}

## Surprises / bugs caught

- {{anything unexpected; bugs caught (in the code OR in the experiment design);
  reproducibility notes}}

## Decision

**Dial {{stays/moves}} at {{X}}%.** {{Rationale tied to AUTOPILOT.md §6. If a
good result did NOT move the dial, explain why — that's the honesty discipline.}}

## Next ticks

- {{the next 1-3 highest-leverage moves this result unlocks}}

## Files

- Script: `research/scripts/{{name}}.py`
- Artifacts: `research/snapshots/tick{{N}}.{json,log}`
