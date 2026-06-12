# AUTOPILOT_PROGRESS

> Append-only ledger. **Prepend** each new tick row at the top (newest first).
> One row ≈ one finding. Dense is good — this is the durable project memory.

## Row format (table form — good for terse, scannable history)

```
| #<tick> | <HH:MM> | **<HEADLINE: what shipped + the decision / dial move>**. <Setup: what you built + how>. <Result: the numbers>. <Decision + rationale: why the dial did/didn't move>. <What it proves / kills>. <Next ticks>. **Files**: <paths>. **Queue**: <todo transitions>. | <track> |
```

Tips:
- Lead with the **decision** (dial stays / moves, finding confirmed / killed),
  not just "ran an experiment."
- Always include the **honest numbers**, even when they're disappointing.
- Note **why the dial did NOT move** when a backtest looked good — that's the
  honesty discipline in action.
- Escape literal `%` as `%%` if your tooling interpolates.

## Example rows

| #3 | 14:20 | **VERIFY: live code reproduces experiment EXACTLY (FAITHFUL) / dial stays 65%%**. Built `verify_live_vs_experiment.py` to confirm the production path matches the validated result. Found + fixed a scoring mismatch; root-caused a look-ahead trap in the *verifier* (gave the experiment unfair future labels). After matching inputs: exact match to 8 dp across 5 test points. Dial STAYS — this clears a TRUST gate, not a RESULT gate. **Files**: verify_live_vs_experiment.py, snapshots/tick3.md. **Queue**: verify→done; 60-run-eval→pending. | research |
| #2 | 09:47 | **BUILD: integrate validated recipe into live system (parallel stream) / dial stays 65%%**. Wired the recipe behind `--use-recipe` writing to SEPARATE state files so the established stream is untouched. Verified gate works + no regression. Dial unchanged: zero realized results yet. **Files**: live_runner.py, run_daily wrapper. **Queue**: integration→done. | build |
| #1 | 08:52 | **PRE-CHECK: parameter drift measured before coding / design corrected**. Rubber-duck flagged that daily-recomputed threshold would drift; measured it → confirmed (spread 23%%). Decision: PIN the parameter instead of recompute. Prevented shipping a drifting integration. **Files**: snapshots/tick1.md. **Queue**: pre-check→done. | research |

---

<!-- Optional: keep a parallel append-only prose log below for longer narrative
     entries that don't fit a table row. Newest at the bottom or top — pick one
     and stay consistent. -->
