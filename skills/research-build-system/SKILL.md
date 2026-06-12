---
name: research-build-system
description: Reusable architecture for autonomous "research → validated build" projects. Codifies the two-track separation (cheap numbered experiments vs a protected live/production stream), the constitution+ledger+plan file trio, the scheduled tick lifecycle (hourly tick + daily ping + resume-on-reboot), an honesty-gated progress dial that only moves on validated/realized results, and rubber-duck-before-build + reproducibility-verification disciplines. Use when setting up any long-running, self-improving project that iterates toward a measurable goal — ML/quant research, optimization pipelines, growth experiments, agent autopilot loops — where you need experiments to graduate into a trustworthy build without overfitting or self-deception. Ships ready-to-copy templates (AUTOPILOT.md, PROGRESS ledger, plan.md, tick snapshot, scheduled-task setup).
---

# Research → Build System

*A battle-tested architecture for autonomous, self-improving projects that must
turn a stream of cheap experiments into a **trustworthy** validated build —
without drifting, overfitting, or lying to yourself with optimistic backtests.*

Distilled from a long-running quant-research autopilot (200+ research ticks,
1000+ experiment artifacts, a live paper-trading bot) but deliberately written
domain-agnostic. The trading specifics are swappable; the skeleton is not.

Use this skill when you're standing up a new project where an agent (or you)
will **repeatedly experiment, validate, and graduate findings into something
that runs for real**, iterating toward a single measurable goal.

---

## The one-sentence model

> A **continuous research loop** with an **honesty gate**, that **graduates**
> validated findings from a cheap experiment track into a protected build track,
> driven by a **scheduled tick lifecycle** and recorded in an **append-only
> ledger**.

---

## The five layers

### 1. Two-track separation (the core idea)

| Track | Purpose | Properties |
|---|---|---|
| **Research track** | Cheap, throwaway-friendly experiments | Numbered, self-contained, each emits `.md` (narrative) + `.json` (machine result) + `.log` (raw run). Failures are fine and informative. |
| **Build/production track** | The thing that runs/deploys for real | Findings only graduate here **after validation**, and even then as an **opt-in parallel stream with separate state**, so a new idea can never break the established one. |

**Why it works:** experiments stay disposable and fast; production stays
stable. You never have to choose between "move fast" and "don't break the live
thing" because they live in different places and graduation is an explicit,
gated step.

Directory shape:
```
<repo>/
  research/
    <CONSTITUTION>.md          # directives (rarely edited)
    <PROGRESS>.md              # append-only ledger (every tick)
    scripts/                   # numbered experiment scripts
    snapshots/                 # per-experiment .md + .json + .log artifacts
    data/                      # frozen/snapshot datasets (reproducibility!)
  <build>/                     # the live/production system
    state/                     # runtime state (logs, results) — committed for audit
  plan.md                      # (or session-state) live plan + the dial banner
```

### 2. The constitution + ledger + plan trio

- **Constitution (`AUTOPILOT.md`)** — the rarely-changed source of truth.
  Mission, **decision authority** (what the agent may do without asking),
  **hard constraints** (the non-negotiables that keep results honest), a
  **quantified progress formula**, explicit **goal criteria**, notification
  rules, and **stop conditions**. This is what keeps an autonomous loop from
  drifting off-mission.
- **Ledger (`AUTOPILOT_PROGRESS.md`)** — append-only. Every tick prepends one
  dense row: what shipped, the numbers, the decision + rationale, files
  touched, next steps. This is the durable memory; you can reconstruct the
  entire project history from it.
- **Plan (`plan.md`)** — the live working plan, headed by a single **dial
  banner** (current % toward goal). Updated at meaningful milestones.
- **Work queue (SQL `todos` table or a `QUEUE.md`)** — queryable list of work
  items with dependencies and status (`pending`/`in_progress`/`done`/`blocked`).

### 3. The scheduled tick lifecycle

Three scheduled jobs, each a self-contained prompt:

| Job | Cadence | Does |
|---|---|---|
| **Tick** | Hourly (or on-demand) | read state → check background jobs → pick the **single highest-leverage move** → execute under constraints → **verify** → log to ledger → commit → exit |
| **Daily ping** | 1×/day | compute progress %, compose a short status message, notify the operator. Gated: skip if nothing shipped. |
| **Resume** | On reboot/login | relaunch the agent session so the loop survives restarts |

Key rule: **a tick ships exactly one focused thing, then exits.** No
open-ended marathons. The loop's power comes from many small, logged,
committed steps — not from one giant session.

### 4. The honesty disciplines (the actual secret sauce)

These are what make the output *trustworthy* instead of a pile of optimistic
results. Skipping these is how self-improving projects fool themselves.

- **Honesty-gated dial** — the headline progress number only moves on
  **validated / realized** results, never on hope or a single good backtest.
  (In the source project the dial sat at 65% for many ticks *despite* strong
  backtests, because no realized forward PnL had landed yet. That discipline is
  the point.)
- **Rubber-duck-before-build** — get an independent critique of any non-trivial
  design *before* writing it. In practice this catches real bugs at the cheapest
  possible moment (e.g. it once predicted a parameter-drift bug that a later
  measurement confirmed exactly — saving a shipped-then-debugged cycle).
- **Reproducibility / verification gates**:
  - **Freeze your inputs.** Live data sources (APIs, scrapes) silently revise.
    Snapshot to disk so an experiment is bit-reproducible. (A whole class of
    "breakthrough then can't-reproduce" bugs come from un-frozen data.)
  - **"Does the live code reproduce the experiment exactly?"** Before promoting,
    verify the production path reproduces the validated result to N decimals on
    matched inputs. Mismatches are usually a real divergence — or a look-ahead
    bug in your *verifier*, which is just as important to catch.
  - **No look-ahead / leakage audits** — any signal computed at time *t* must
    have been available at *t*.
- **Commit every meaningful tick** with a detailed message — full audit trail,
  one commit ≈ one finding.
- **Separate gates: trust vs result.** "Is the code faithful?" (verification)
  and "did it hit the number?" (realized result) are different gates. Clearing
  one does not move the other.

### 5. Adapting to a new domain

Swap the domain specifics; keep the skeleton:

| Generic slot | Quant example | Your project |
|---|---|---|
| "experiment" | a backtest sweep | ? |
| "validation" | walk-forward + frozen panel + N-seed | ? |
| "realized result" | live paper-trade PnL | ? |
| "the dial metric" | %/mo return vs target | ? |
| "hard constraints" | realistic friction, no leakage | ? |
| "build track" | the live signal bot | ? |

If you can fill that table, this system fits.

---

## Quickstart — stand up a new project

1. **Copy the templates** (in `templates/`) into your repo:
   - `AUTOPILOT.md` → `research/AUTOPILOT.md` (fill in `{{PLACEHOLDERS}}`)
   - `AUTOPILOT_PROGRESS.md` → `research/AUTOPILOT_PROGRESS.md`
   - `plan.md` → repo root or your agent's session-state
   - `tick-snapshot.md` → keep as the per-experiment writeup template
2. **Define your dial** — pick the single number and the formula in `AUTOPILOT.md §6`.
3. **Create the directory split**: `research/scripts/`, `research/snapshots/`,
   `research/data/`, and your `<build>/` track.
4. **Set up the work queue** — SQL `todos` table (preferred) or `QUEUE.md`.
5. **Wire the scheduled jobs** — see `templates/scheduled-tasks.md` for the
   Windows Task Scheduler recipe (tick + ping + resume). On other OSes use cron
   / systemd timers / launchd.
6. **Write the first constitution rules** that encode YOUR honesty constraints
   (what would make a result fake in your domain? forbid it explicitly).
7. **Run tick #1**: pick one experiment, run it, write the snapshot, prepend the
   ledger row, commit. Repeat.

---

## Anti-patterns this system is designed to prevent

- ❌ **Backtest euphoria** — celebrating a number that hasn't survived
  out-of-sample / realized validation. (Fix: honesty-gated dial.)
- ❌ **Un-frozen data** — "I had a breakthrough yesterday and can't reproduce
  it today." (Fix: snapshot inputs.)
- ❌ **Build-track contamination** — a half-baked idea breaks the thing that
  runs for real. (Fix: opt-in parallel stream + separate state.)
- ❌ **Marathon sessions** — one giant untracked session you can't audit or
  resume. (Fix: small ticks, append-only ledger, commit each.)
- ❌ **Verifier self-deception** — your validation gives the experiment an
  unfair advantage (look-ahead, leakage) and reports a false pass/fail. (Fix:
  match inputs exactly; treat a verifier as code that also needs review.)
- ❌ **Silent drift** — the autonomous loop wanders off-mission. (Fix:
  constitution with decision authority + stop conditions, re-read each tick.)

---

## Files in this skill

| File | Use |
|---|---|
| `SKILL.md` | This reference. |
| `templates/AUTOPILOT.md` | Constitution template (mission, authority, constraints, progress formula, goals, stop conditions). |
| `templates/AUTOPILOT_PROGRESS.md` | Append-only ledger template + row format. |
| `templates/plan.md` | Live plan with the dial banner. |
| `templates/tick-snapshot.md` | Per-experiment writeup template (.md companion to .json/.log). |
| `templates/scheduled-tasks.md` | Windows Task Scheduler recipe for tick + ping + resume (with cron/systemd notes). |

*Last verified: 2026-06-12. Derived from the LSTM_AI_Stock_Predictor autopilot
(session 1e79e3d8).*
