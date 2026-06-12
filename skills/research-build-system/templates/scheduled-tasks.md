# Scheduled jobs — tick + ping + resume

The loop runs on three scheduled jobs. This file gives a Windows Task Scheduler
recipe (the source project's platform) plus cron / systemd / launchd notes.

The agent is **GitHub Copilot CLI** invoked non-interactively. The key flags:

```
copilot -p "<PROMPT>" --allow-all --no-ask-user --resume=<SESSION_ID>
```
- `-p` runs a single prompt non-interactively.
- `--allow-all` (a.k.a. `--yolo`) auto-approves tools (required for unattended).
- `--no-ask-user` stops it pausing to ask questions.
- `--resume=<id>` keeps every tick in the SAME session so context + plan persist.
  (Get the id from `/session` in an interactive run, or omit to start fresh.)

> ⚠ Quoting on Windows: pass the prompt as a single quoted string. Beware
> `-Command "...-C..."`: Commander packs short flags, so a literal `-Command`
> can be misparsed as `-C ommand`. Quote tightly.

---

## The three jobs

### 1. Tick (hourly) — the work cycle
Prompt (store as a scheduled-prompt or pass via `-p`):
```
[AUTOPILOT TICK] Hourly work cycle.
1. Read research/AUTOPILOT.md + research/AUTOPILOT_PROGRESS.md + plan.md head.
2. Check background jobs (list processes / shells).
3. Query the work queue for ready items (pending, deps satisfied).
4. If background jobs finished: aggregate, update PROGRESS.md, commit.
5. Pick the single highest-leverage move toward the goal (AUTOPILOT.md §6).
6. Execute under §4 hard constraints. Rubber-duck non-trivial designs FIRST.
7. Verify the result (reproducibility / no-leakage / matched-input check).
8. Update work-queue status.
9. Append a Tick #N row to PROGRESS.md; commit if files changed.
10. Ship ONE focused thing, then exit. Do NOT notify here (that's the ping's job)
    unless the goal is achieved or a STOP condition is hit.
```

### 2. Daily ping — operator status
```
[DAILY PING] 1. Read PROGRESS.md (recent ticks) + AUTOPILOT.md §6 formula.
2. Compute current % and delta vs yesterday.
3. Compose a <=500-char status: "Day-X — Y% (Δ+Z today). Top result: ...
   Latest ship: ... Next: ...". Include blockers.
4. If §7 goal achieved → upgrade to the BIG announcement instead.
5. Send via {{operator channel}}. Update PROGRESS with today's % + note.
GATE: skip if today's commits == 0 AND % delta == 0.
```

### 3. Resume (on reboot/login) — survive restarts
A launcher script that relaunches the agent in the project dir, resuming the
session, so the loop continues after a reboot.

---

## Windows Task Scheduler (PowerShell)

```powershell
$py   = "copilot"            # or full path to the CLI
$repo = "C:\path\to\project"
$sid  = "<SESSION_ID>"

# Tick — every hour
$tickPrompt = '<paste the TICK prompt above>'
$action  = New-ScheduledTaskAction -Execute "powershell.exe" `
  -Argument "-NoProfile -ExecutionPolicy Bypass -Command `"cd '$repo'; copilot -p '$tickPrompt' --allow-all --no-ask-user --resume=$sid`""
$trigger = New-ScheduledTaskTrigger -Once -At (Get-Date) `
  -RepetitionInterval (New-TimeSpan -Hours 1)
Register-ScheduledTask -TaskName "MyProj-Tick" -Action $action -Trigger $trigger `
  -Description "Hourly research-build tick"

# Daily ping — once a day
$pingTrigger = New-ScheduledTaskTrigger -Daily -At 9am
# ...same shape with the PING prompt, TaskName "MyProj-DailyPing"

# Resume — at logon
$resumeTrigger = New-ScheduledTaskTrigger -AtLogOn
# ...action runs a resume launcher script, TaskName "MyProj-Resume"
```

> The source project used a small `*.ps1` launcher (hidden window) rather than a
> giant inline `-Command` to avoid quoting pain. Recommended.

---

## Non-Windows equivalents

- **cron** (Linux/macOS):
  ```
  0 * * * *  cd /path/to/project && copilot -p "$(cat tick_prompt.txt)" --allow-all --no-ask-user --resume=SID >> logs/tick.log 2>&1
  0 9 * * *  cd /path/to/project && copilot -p "$(cat ping_prompt.txt)" --allow-all --no-ask-user --resume=SID
  @reboot    cd /path/to/project && <resume launcher>
  ```
- **systemd timers** — a `.service` + `.timer` pair per job (tick `OnCalendar=hourly`,
  ping `OnCalendar=*-*-* 09:00:00`, resume `WantedBy=default.target` after boot).
- **launchd** (macOS) — `StartCalendarInterval` for tick/ping, `RunAtLoad` for resume.

---

## Operational notes (learned the hard way)

- **One focused thing per tick.** Don't let a tick sprawl; the value is in many
  small committed steps.
- **Commit inside the tick**, not in a batch later — the ledger and git history
  must stay in lockstep.
- **The ping is the only routine notifier.** Ticks stay silent unless goal-met
  or STOP — otherwise you train yourself to ignore pings.
- **Background long jobs**, then end the tick; pick up results next tick. Don't
  block a whole tick waiting.
- **Check that the schedule only fires when the machine is on / user logged in**
  if your agent needs a desktop session; otherwise use a service account.
