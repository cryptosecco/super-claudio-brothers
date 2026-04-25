```
   ╔══════╗     ███████╗██╗   ██╗██████╗ ███████╗██████╗
  ╔╝██████╚╗    ██╔════╝██║   ██║██╔══██╗██╔════╝██╔══██╗
  ║██░██░██║    ███████╗██║   ██║██████╔╝█████╗  ██████╔╝
  ╚═══╗╔═══╝    ╚════██║██║   ██║██╔═══╝ ██╔══╝  ██╔══██╗
      ║║        ███████║╚██████╔╝██║     ███████╗██║  ██║
      ╚╝        ╚══════╝ ╚═════╝ ╚═╝     ╚══════╝╚═╝  ╚═╝

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

## First run

The first time the plugin loads, you get one onboarding message with three options:

- **`ok`** — enable both brothers, start watching.
- **`disable`** — install but stay silent until you opt in.
- **`install hook`** — confirm the hook is wired (already automatic in most setups).

After that, the plugin is invisible until the 4th hit.

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
