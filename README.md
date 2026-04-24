```
                      ___           ___           ___           ___
                     /   \         /   \         /   \         /   \
                    | o o |       | o o |       | o o |       | o o |
                     \___/         \___/         \___/         \___/
                     |   |         |   |         |   |         |   |
                     |___|         |___|         |___|         |___|


      ███████╗██╗   ██╗██████╗ ███████╗██████╗
      ██╔════╝██║   ██║██╔══██╗██╔════╝██╔══██╗
      ███████╗██║   ██║██████╔╝█████╗  ██████╔╝
      ╚════██║██║   ██║██╔═══╝ ██╔══╝  ██╔══██╗
      ███████║╚██████╔╝██║     ███████╗██║  ██║
      ╚══════╝ ╚═════╝ ╚═╝     ╚══════╝╚═╝  ╚═╝

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


                    Tu la lanci, e lei pensa al resto.
```

# Super Claudio Brothers

> **Tu la lanci, e lei pensa al resto.**

Super Claudio Brothers notices the workflows you repeat in Claude Code — and turns them into reusable skills before you have to ask.

No configuration. No rituals. Installed once, invisible until the 4th time you do the same thing.

---

## What you stop doing

- **Re-explaining the same investigation ritual** every time a new bug looks like the last one.
- **Rebuilding the same setup sequence** for the 7th project this month.
- **Typing the same 4 commands in the same order** you've already typed a hundred times.

## What happens instead

Super Claudio Brothers watches silently across sessions. The 4th time you repeat a multi-step workflow — regardless of project, language, or subsystem — it pauses at the end of the turn and shows:

```
🍄 PATTERN DETECTED (4th occurrence)

Pattern: <what it noticed>
Observed: <first date> → <last date>, 4 occurrences

Proposed skill:
  • Name:        itsme-<slug>
  • Description: "Use when <trigger>"
  • Location:    ~/.claude/skills/itsme-<slug>/SKILL.md

Create now or decline?
```

You say yes, decline, or ignore. If yes, a new `itsme-<name>` skill appears in your toolbox. Next time, one command does what used to take four.

If you decline — the skill remembers forever. No re-prompting.

## 30-second proof

_Demo GIF coming in v0.1.1. Until then, see [`skills/super-claudio-brothers/SKILL.md`](skills/super-claudio-brothers/SKILL.md) for the full behavior spec._

## Who this is for

- You **work in Claude Code daily** and you've caught yourself repeating context.
- You **don't want to stop and write automation** — you want automation to notice itself.
- You **trust a tool that asks before acting** and respects "no" forever.
- You're **tired of teaching every new session the same things**.

---

## Install

```bash
/plugin marketplace add cryptosecco/super-claudio-brothers
/plugin install super-claudio-brothers@super-claudio-brothers
```

That's it. The skill and its `SessionStart` hook ship together — no manual `settings.json` edits.

## What you get

- A skill that observes every Claude Code session, matching on **shape** of workflow, not surface details.
- A `SessionStart` hook that loads your tracked-patterns index into every new session so cross-session counts are always visible.
- Zero background daemons. Zero telemetry. Everything runs inside your Claude Code session and writes to files under your home directory.

## How it works

| Occurrence | Action |
|------------|--------|
| 1st | Ignored — normal activity |
| 2nd | Noted internally, nothing written |
| 3rd | **Silent tracking** — creates `~/.claude/patterns/<slug>.md` |
| 4th | **Soft-blocking proposal** at end of turn with full draft of the new skill |

Patterns match on **shape**, not surface: a bug investigation across different subsystems still counts as the same pattern if the 4-step flow is identical. **Goal category** (`bug-fix`, `refactor-design`, `audit`, `migration`, `performance-triage`, `review`, `investigation`, `scaffold`) must match, or two similar workflows are split into separate patterns.

Full behavior spec, fuzzy-matching rules, drift handling, and taxonomy: [`skills/super-claudio-brothers/SKILL.md`](skills/super-claudio-brothers/SKILL.md).

## File layout

After first use, your home directory will contain:

```
~/.claude/
├── patterns/
│   ├── PATTERNS.md         # index of tracked patterns (one line each)
│   ├── <pattern-slug>.md   # one file per pattern — count, status, observations
│   ├── .onboarded          # flag: onboarding completed
│   └── .disabled           # flag: skill silenced (create to disable)
└── skills/
    └── itsme-<slug>/       # skills crystallized from your patterns — user-owned
        └── SKILL.md
```

The `itsme-*` skills are yours — they live outside the plugin cache and survive uninstall.

## Controls

- **Disable:** `touch ~/.claude/patterns/.disabled`
- **Re-enable:** `rm ~/.claude/patterns/.disabled`
- **Force re-onboarding:** `rm ~/.claude/patterns/.onboarded`

Declining a specific pattern is permanent per-pattern — the skill writes `status: declined` in that pattern's file and never re-prompts. You can manually flip it back to `tracking` by editing the file if you change your mind.

## First run

At first activation you'll see a 🍄 onboarding block that explains the system in plain language. Reply with:

- `ok` → continue, skill becomes active
- `disable` → kill switch on, skill stays silent until you re-enable
- `install hook` → no-op, the hook already ships with the plugin

## Troubleshooting

**Skill doesn't fire when expected.** Open `/hooks` menu once to reload config, or restart Claude Code. The `SessionStart` hook needs a fresh session to activate.

**`~/.claude/patterns/PATTERNS.md` is empty after several workflows.** Expected until the 3rd occurrence of the same pattern — tracking starts at 3, proposals fire at 4.

**A pattern was declined but I changed my mind.** Open `~/.claude/patterns/<slug>.md` and change `status: declined` back to `status: tracking`. Next occurrence will resume counting.

## Uninstall

```bash
/plugin uninstall super-claudio-brothers@super-claudio-brothers
```

This removes the plugin and the `SessionStart` hook. Your `~/.claude/patterns/` files and any `itsme-*` skills you approved stay — they're yours.

## License

MIT — see [LICENSE](LICENSE).

## Credits

Built by [Danilo Giudice](https://github.com/cryptosecco). Inspired by the feeling of doing the same thing for the 4th time and realizing you've already paid the cost of automation just by repeating yourself.
