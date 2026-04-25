```
   ▄████████▄    ███████╗██╗   ██╗██████╗ ███████╗██████╗
  ████░██░███    ██╔════╝██║   ██║██╔══██╗██╔════╝██╔══██╗
 █████████████   ███████╗██║   ██║██████╔╝█████╗  ██████╔╝
 ████░████░██    ╚════██║██║   ██║██╔═══╝ ██╔══╝  ██╔══██╗
  ▀█████████▀    ███████║╚██████╔╝██║     ███████╗██║  ██║
      ▐██▌       ╚══════╝ ╚═════╝ ╚═╝     ╚══════╝╚═╝  ╚═╝

                ██████╗██╗      █████╗ ██╗   ██╗██████╗ ██╗ ██████╗
               ██╔════╝██║     ██╔══██╗██║   ██║██╔══██╗██║██╔═══██╗
               ██║     ██║     ███████║██║   ██║██║  ██║██║██║   ██║
               ██║     ██║     ██╔══██║██║   ██║██║  ██║██║██║   ██║
               ╚██████╗███████╗██║  ██║╚██████╔╝██████╔╝██║╚██████╔╝
                ╚═════╝╚══════╝╚═╝  ╚═╝ ╚═════╝ ╚═════╝ ╚═╝ ╚═════╝

               ██████╗ ██████╗  ██████╗ ████████╗██╗  ██╗███████╗██████╗ ███████╗
               ██╔══██╗██╔══██╗██╔═══██╗╚══██╔══╝██║  ██║██╔════╝██╔══██╗██╔════╝
               ██████╔╝██████╔╝██║   ██║   ██║   ███████║█████╗  ██████╔╝███████╗
               ██╔══██╗██╔══██╗██║   ██║   ██║   ██╔══██║██╔══╝  ██╔══██╗╚════██║
               ██████╔╝██║  ██║╚██████╔╝   ██║   ██║  ██║███████╗██║  ██║███████║
               ╚═════╝ ╚═╝  ╚═╝ ╚═════╝    ╚═╝   ╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝╚══════╝
```

# Super Claudio Brothers

> *🍄 it's me claudio*

This plugin spawns **two agents** inside Claude Code — Claudio and Dario — that work in tandem to turn your repeated workflows into reusable skills. You keep shipping; they take care of the bookkeeping.

---

## The two brothers

### 🍄 Claudio — The Watcher

**What he does.** Runs silently across every session. Annotates recurring multi-step workflows into `~/.claude/patterns/`. Never speaks. Never interrupts. Just counts.

- 1st time you do something: ignored.
- 2nd time: noted internally.
- 3rd time: a quiet tracking file appears.

**What he solves.** You stop re-explaining the same workflow to a new session every morning. The pattern memory lives outside the conversation, so context resets don't erase it.

### 🍄 Dario — The Proposer

**What he does.** Wakes up on the 4th occurrence. At end of turn, presents a soft-blocking block with a complete skill proposal — name, description, steps, trigger, type.

- You approve → a new skill lands at `~/.claude/skills/itsme-<slug>/`.
- You decline → the pattern is shelved forever. No re-prompting.

**What he solves.** You never have to decide *if* a workflow is worth crystallizing or sit down to write a skill by hand. By the 4th repetition, Dario has already drafted it for you.

### Naming convention

Every skill the brothers crystallize is saved under `~/.claude/skills/itsme-<slug>/`. The `itsme-` prefix is the signature — one glance at your skills folder and you know which ones came from the brothers and which you wrote yourself.

---

## Why it stays out of your way

- **Every repetition compounds.** The 4th time you do something, it's already a skill you own.
- **Everything stays on your machine.** No telemetry. No cloud. No daemons. Just two flag files and a folder under `~/.claude/`.

---

## Install

```bash
/plugin marketplace add cryptosecco/super-claudio-brothers
/plugin install super-claudio-brothers@super-claudio-brothers
```

That's it. The enhanced hook ships with the plugin — no `settings.json` edit, no manual wiring.

---

## First run — choose how the brothers operate

The first time the skill activates after install, Claudio runs two pre-flight checks before doing anything else:

1. Does `~/.claude/patterns/.disabled` exist? → if yes, the brothers stay silent.
2. Does `~/.claude/patterns/.onboarded` exist? → if no, present onboarding (this happens **once**, ever).

The onboarding message looks like this:

```
🍄 Super Claudio Brothers — first run

Two brothers will now watch your workflow:
  • Claudio — the Watcher. Observes patterns silently, writes to ~/.claude/patterns/
  • Dario   — the Proposer. At the 4th repetition, offers a ready-made skill

How it works:
  • 1st–2nd time you do something: I stay silent.
  • 3rd time:  I silently start tracking in ~/.claude/patterns/.
  • 4th time:  after finishing the task, I propose a new skill
               named itsme-<slug> that you can approve or decline.

Reply "ok" to proceed, "disable" to turn me off, or "install hook"
for the upgrade.
```

You pick one of three replies. Each writes flag files under `~/.claude/patterns/` and never prompts you again.

| Reply | What happens | When to choose it |
|-------|--------------|-------------------|
| **`ok`** | Writes `.onboarded`. Brothers go live in within-session mode. | Default — try it for a few days, then upgrade if you want cross-session memory. |
| **`disable`** | Writes both `.onboarded` and `.disabled`. Brothers stay silent until you `rm ~/.claude/patterns/.disabled`. | You want the plugin installed but inactive for now. |
| **`install hook`** | Invokes `/update-config` to wire a `SessionStart` hook into `settings.json`, then writes `.onboarded`. | You want full cross-session coverage from day one. |

### Coverage modes

| Mode | What it does | Coverage |
|------|--------------|----------|
| **Default** (just `ok`) | At session start, Claudio reads `~/.claude/patterns/PATTERNS.md` from inside the skill. | ~80% — works reliably within a session; cross-session counts can occasionally be missed if the skill doesn't auto-activate on the first turn. |
| **With hook** (`install hook` or pre-wired) | A `SessionStart` hook in `settings.json` runs `cat ~/.claude/patterns/PATTERNS.md` automatically before any conversation begins, so tracked patterns are always in context. | 100% — every session starts with full pattern memory loaded. |

The hook is a single line in `settings.json` and ships pre-wired with the plugin — most installs already have it. If you skipped it during onboarding and change your mind later, just say *"install the super-claudio-brothers hook"* in any session.

### Changing your mind later

| Want to… | Do this |
|----------|---------|
| Pause the brothers | `touch ~/.claude/patterns/.disabled` |
| Resume them | `rm ~/.claude/patterns/.disabled` |
| Upgrade to hook coverage | Ask Claude to *"install the super-claudio-brothers hook"* |
| Re-do onboarding | `rm ~/.claude/patterns/.onboarded` then start a new session |

---

## How it works

### The 4-hit threshold

| Occurrence | What Claudio does                              | What you see             |
|------------|------------------------------------------------|--------------------------|
| 1st        | Ignored                                        | Nothing                  |
| 2nd        | Noted internally                               | Nothing                  |
| 3rd        | Writes a silent tracking file                  | Nothing                  |
| 4th        | Dario speaks — soft-blocking 🍄 proposal       | A skill, ready           |

### Patterns match on shape, not surface

The same abstract flow across different subsystems counts as the same pattern. "Reproduce the bug, isolate the failing module, write the fix, add a regression test" is one pattern whether the subsystem is auth or billing.

### Goal categories

Patterns are scoped by intent. If the goal differs, the pattern splits:

`bug-fix` · `refactor-design` · `audit` · `migration` · `performance-triage` · `review` · `investigation` · `scaffold`

Full behaviour spec, fuzzy-matching rules, drift handling, declined-permanence: [`skills/super-claudio-brothers/SKILL.md`](skills/super-claudio-brothers/SKILL.md).

---

## File layout

Everything lives under `~/.claude/`, owned by you:

- `~/.claude/patterns/PATTERNS.md` — the index
- `~/.claude/patterns/<slug>.md` — one file per tracked pattern
- `~/.claude/patterns/.onboarded` — first-run flag
- `~/.claude/patterns/.disabled` — kill switch
- `~/.claude/skills/itsme-<slug>/` — crystallized skills, yours to edit

---

## Controls

Silence Claudio:

```bash
touch ~/.claude/patterns/.disabled
```

Bring him back:

```bash
rm ~/.claude/patterns/.disabled
```

No daemon to stop. No process to kill. Just a flag file.

---

## Troubleshooting

**The proposal never fired.**
Open `/hooks` to confirm the hook is registered, or restart Claude Code. The hook ships with the plugin but some environments cache older settings.

**`~/.claude/patterns/` is empty.**
Expected. Nothing is written until the 3rd occurrence of a pattern. Keep working — Claudio is counting.

**I declined a pattern and regret it.**
Open `~/.claude/patterns/<slug>.md` and flip `status: declined` back to `status: tracking`. Dario will reconsider on the next hit.

**The skill fires at the wrong time.**
It lives in `~/.claude/skills/itsme-<slug>/` — edit the trigger or description directly. It's yours now.

---

## Uninstall

```bash
/plugin uninstall super-claudio-brothers@super-claudio-brothers
```

Your patterns and skills stay where they are. Delete them manually if you want a clean slate.

---

## License

MIT — see [LICENSE](LICENSE).

---

## Credits

Built by [Danilo Giudice](https://github.com/cryptosecco) for people who keep typing the same five steps every morning and wish someone were paying attention.

The mushroom 🍄 is the mark. If you see it at the end of a turn, Dario has something for you.
