# copilot-skills

A personal collection of [GitHub Copilot CLI](https://docs.github.com/copilot)
**skills** — reusable reference docs + templates that I pull into projects.

## Skills

| Skill | What it's for |
|-------|---------------|
| [`research-build-system`](skills/research-build-system/) | Architecture for autonomous "research → validated build" projects: two-track separation, constitution+ledger+plan trio, scheduled tick lifecycle, honesty-gated progress dial, and reproducibility/verification disciplines. Ships ready-to-copy templates. |

## How Copilot CLI skills work

A skill is a folder containing a `SKILL.md` with YAML frontmatter (`name` +
`description`). Copilot CLI loads skills from `~/.copilot/skills/<name>/` (personal)
or a project's `.github/skills/`. The `description` tells the agent when to
auto-invoke it.

## Install (pull a skill into your environment)

**Option A — symlink (stays in sync with the repo):**
```powershell
# Windows (run as admin or with Developer Mode on)
git clone https://github.com/Nicolassalazar-pro/copilot-skills.git
New-Item -ItemType SymbolicLink `
  -Path "$env:USERPROFILE\.copilot\skills\research-build-system" `
  -Target "<clone-path>\copilot-skills\skills\research-build-system"
```
```bash
# macOS / Linux
git clone https://github.com/Nicolassalazar-pro/copilot-skills.git
ln -s "$PWD/copilot-skills/skills/research-build-system" \
      "$HOME/.copilot/skills/research-build-system"
```

**Option B — copy (snapshot, no auto-sync):**
```powershell
Copy-Item -Recurse "<clone-path>\copilot-skills\skills\research-build-system" `
  "$env:USERPROFILE\.copilot\skills\"
```

Then in an active Copilot CLI session:
```
/skills reload
/skills info research-build-system
```

## Use the templates directly (no skill install needed)

Each skill's `templates/` folder holds copy-paste-ready files. For
`research-build-system`, drop them into a new project's `research/` dir and fill
in the `{{PLACEHOLDERS}}`.

## Adding a new skill

```
skills/<your-skill>/
  SKILL.md            # frontmatter: name + description (when to use)
  templates/          # optional copy-paste assets
  <supporting docs>   # optional deep-dive appendices
```
Commit, push, done. Pull it into any machine via the install steps above.

---

*Maintained by @Nicolassalazar-pro.*
