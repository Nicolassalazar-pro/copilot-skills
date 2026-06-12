# AUTOPILOT — {{PROJECT_NAME}}

> **Status:** {{ACTIVE / PAUSED}} since {{DATE}}
> **Owner:** {{OPERATOR}}
> **Goal (base):** {{ONE-SENTENCE MEASURABLE GOAL, e.g. "validated 2%/mo growth under realistic constraints"}}
> **Goal (stretch, optional):** {{HIGHER BAR THAT ACTIVATES ONLY AFTER BASE IS LOCKED}}

---

## 1. Mission

{{2-4 sentences. What is this project driving toward, and what is the absolute
constraint that makes a result "real" rather than illusory in this domain?}}

"Validated" means: {{your domain's definition — e.g. ">=6 months out-of-sample,
statistically distinguishable from luck, honest execution assumptions". Be
specific; this word does a lot of work.}}

---

## 2. Operator Directives

| Setting | Value |
|---------|-------|
| Cadence | {{e.g. Continuous — work as soon as previous task completes}} |
| Decision authority | {{Full / Scoped — see §3}} |
| Compute budget | {{Unlimited / capped}} |
| Notification frequency | {{e.g. one daily progress ping + goal-achieved announcement only}} |
| Pre-authorized actions | {{things the agent may do without asking}} |
| Failure tolerance | {{e.g. stop after N consecutive same-idea failures, then notify}} |
| Off-limits | {{the hard "never do this" list}} |

---

## 3. Decision Authority

### The agent MAY do without asking:
- Pick the next work item (highest leverage, deps satisfied)
- Write, refactor, and commit experiment scripts
- Run experiments / validations / sweeps
- Update `{{PROGRESS}}.md`, `plan.md`, the work queue
- Modify the build track's logic **behind an opt-in flag / parallel stream**
- Launch background jobs; spawn sub-agents (incl. rubber-duck critique)
- Commit with descriptive messages
- {{domain-specific allowances}}

### The agent MUST STOP and notify the operator if:
- {{N}} consecutive same-idea failures (wrong direction?)
- Any attempt to cross an off-limits line (§2)
- Data integrity issue that can't be auto-resolved
- Repository in a broken/uncommittable state
- Operator explicitly says PAUSE

---

## 4. Hard Constraints (NON-NEGOTIABLE)

These are what keep results honest. Relaxing any requires explicit operator approval.

1. **{{Realistic execution / measurement}}** — {{e.g. use realistic costs, no idealized fills}}
2. **No look-ahead / leakage** — any signal at time t must be available at t.
3. **Freeze live-data inputs** — snapshot APIs/scrapes to disk; experiments must be bit-reproducible.
4. **Honest reporting** — report the conservative/realistic number as the headline; idealized numbers only as labeled diagnostics.
5. **Verification before promotion** — the build track must reproduce the validated experiment to {{N}} decimals on matched inputs before a finding graduates.
6. {{domain-specific constraints}}

---

## 5. Workflow per Tick

1. Read this file + `{{PROGRESS}}.md` + `plan.md` head.
2. Check background jobs / running processes.
3. Query the work queue for ready items (status=pending, deps satisfied).
4. If background jobs finished → aggregate results, update ledger, commit.
5. Pick the **single highest-leverage move** toward the goal (§6).
6. Execute under §4 constraints. **Rubber-duck non-trivial designs first.**
7. **Verify** the result (reproducibility / no-leakage / matched-input check).
8. Update work-queue status as you go.
9. Append a ledger row; commit if files changed.
10. Exit. (One focused thing per tick.)

---

## 6. Progress Estimation (THE DIAL)

Composite progress % = weighted blend (tune weights to your project):

- **{{40}}%** — best **validated** result vs the target metric (capped 100%)
- **{{30}}%** — {{coverage: experiments/arenas validated / total planned}}
- **{{15}}%** — {{critical components integrated / total}}
- **{{15}}%** — {{statistical validation: OOS months / required}}

**HONESTY RULE:** the dial only moves up on **validated / realized** results.
A strong un-validated experiment ADDS CONFIDENCE but DOES NOT move the number.

Track the current % in `plan.md`'s banner; report it in the daily ping.

---

## 7. Goal Achievement Criteria (the BIG-signal trigger)

### 7a. BASE GOAL
All must hold:
1. {{metric}} ≥ {{target}} over {{window}}, realistic assumptions
2. {{secondary metric, e.g. Sharpe ≥ 1.0 / p95 latency < X}}
3. Holds across {{≥3 seeds / repeated runs}} (variance < {{X}})
4. {{risk bound, e.g. max drawdown ≤ 15%}}
5. **Realized** confirmation: ≥{{N}} {{days/runs}} of forward/live data confirms it

When ALL hold → celebratory notification, then (optionally) switch to §7b.

### 7b. STRETCH GOAL (optional, activates only after 7a is locked)
{{same structure, higher bar}}

---

## 8. State Files

| File | Purpose | Cadence |
|------|---------|---------|
| `AUTOPILOT.md` | This constitution | Rare |
| `{{PROGRESS}}.md` | Append-only ledger | Every tick |
| `plan.md` | Live plan + dial banner | Milestones |
| work queue (SQL/`QUEUE.md`) | Work items + deps | Every tick |
| `research/snapshots/*` | Per-experiment artifacts | Per experiment |

---

## 9. Notification Channels

| Channel | When |
|---------|------|
| {{operator channel}} | Daily progress ping + goal-achieved + blocker only |
| Ledger | Every tick |

---

## 10. Stop Conditions

Keep going unless: {{N}} consecutive same-idea failures · data integrity broken ·
repo broken · operator says PAUSE.
