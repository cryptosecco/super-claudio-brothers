<p align="center">
  <img src="banner.svg" alt="Super Claudio Brothers" width="640" />
</p>

# Super Claudio Brothers

> *Tu la lanci, e lei pensa al resto.*

A Claude Code plugin with two agents working in tandem. One watches. One proposes. You keep shipping.

---

## The two brothers

Every morning you open a new Claude Code session and re-explain the same workflow. Again. Super Claudio Brothers ends that loop with a pair of agents, each with exactly one job.

### Brother #1 — The Watcher

Runs silently across every session. Annotates recurring multi-step workflows into `~/.claude/patterns/`. Never speaks. Never interrupts. Just counts.

- 1st time you do something: ignored.
- 2nd time: noted internally.
- 3rd time: a quiet tracking file appears.

### Brother #2 — The Proposer

Wakes up on the 4th occurrence. At end of turn, presents a soft-blocking block with a complete skill proposal — name, description, steps, trigger, type. The mushroom 🍄 marks every proposal.

- You approve → a new skill lands at `~/.claude/skills/itsme-<slug>/`.
- You decline → the pattern is shelved forever. No re-prompting.

---

## What changes for you

- **You stop explaining yourself to a new session every morning.** The Watcher remembers so you don't have to.
- **Every repetition compounds.** The 4th time you do something, it's already a skill you own.
- **Two agents watch your workflow while you keep shipping.** No context-switching, no manual bookkeeping.
- **Everything stays on your machine.** No telemetry. No cloud. No daemons.

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

| Occurrence | What the Watcher does                          | What you see         |
|------------|------------------------------------------------|----------------------|
| 1st        | Ignored                                        | Nothing              |
| 2nd        | Noted internally                               | Nothing              |
| 3rd        | Writes a silent tracking file                  | Nothing              |
| 4th        | The Proposer speaks — soft-blocking 🍄 proposal | A skill, ready       |

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

Silence the Watcher:

```bash
touch ~/.claude/patterns/.disabled
```

Bring it back:

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
Expected. Nothing is written until the 3rd occurrence of a pattern. Keep working — the Watcher is counting.

**I declined a pattern and regret it.**
Open `~/.claude/patterns/<slug>.md` and flip `status: declined` back to `status: tracking`. The Proposer will reconsider on the next hit.

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

The mushroom 🍄 is the mark. If you see it at the end of a turn, a brother has something for you.
