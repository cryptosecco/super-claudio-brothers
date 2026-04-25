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

> *🍄 "it's me claudio*"

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-8B5CF6?logo=anthropic)](https://docs.claude.com/en/docs/claude-code/plugins)
[![Version](https://img.shields.io/github/v/tag/cryptosecco/super-claudio-brothers?label=version&color=brightgreen)](https://github.com/cryptosecco/super-claudio-brothers/releases)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/cryptosecco/super-claudio-brothers?style=social)](https://github.com/cryptosecco/super-claudio-brothers/stargazers)

This plugin spawns **two agents** inside Claude Code — Claudio and Dario — that work in tandem to turn your repeated workflows into reusable skills. You keep shipping; they take care of the bookkeeping.

---

## The two brothers

### 🍄 Claudio — The Watcher

**What he does.** Runs silently across every session. Annotates recurring multi-step workflows into `~/.claude/patterns/`. Never speaks. Never interrupts. Just counts. (See [the 4-hit threshold](#the-4-hit-threshold) below for the exact rhythm.)

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

## Requirements

- **[Claude Code](https://docs.claude.com/en/docs/claude-code)** with the `/plugin` subsystem (any version that supports plugin marketplaces)
- **macOS, Linux, or Windows (WSL)** — anywhere Claude Code runs
- **No additional dependencies** — pure Markdown skill + JSON hook config, zero runtime, zero daemons

> New to Claude Code plugins? `/plugin` is a built-in slash command that installs marketplaces and skills directly from GitHub. Run `/plugin help` inside Claude Code if you want to see the full subsystem.

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

## Quick start — what you'll actually see

After install + onboarding, the brothers stay invisible while you work. The first time something interesting happens is at the **4th repetition** of a multi-step pattern. At end of turn, Dario appends a single block like this:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🍄 PATTERN DETECTED (4th occurrence)

Pattern: Reproduce bug → isolate failing module → write fix → add regression test
Observed: 2026-04-12 → 2026-04-25, 4 occurrences

Proposed skill:
  • Name:        itsme-bug-fix-with-regression
  • Description: "Use when fixing a confirmed bug, before writing code"
  • Type:        discipline
  • Location:    ~/.claude/skills/itsme-bug-fix-with-regression/SKILL.md

Create now (invokes /superpowers:writing-skills) or decline?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

You reply `yes` → the skill is generated and saved under `~/.claude/skills/itsme-bug-fix-with-regression/`. Next time the same pattern starts, the skill auto-activates and runs the workflow for you.

You reply `no` → the pattern is marked declined and Dario never re-proposes it.

That's the whole user-facing surface of the plugin. Everything else is invisible.

> *📸 Static screenshot and animated demo (asciinema) coming in v0.1.1 — see [Roadmap](#roadmap).*

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

## Roadmap

| Version | Status | What's in it |
|---------|--------|--------------|
| **0.1.0** | ✅ Released (2026-04-24) | Initial plugin — skill, hook, manifests, full README |
| **0.1.1** | 🚧 In progress | Animated demo (asciinema + agg) and static screenshot of the 4th-hit proposal block, both in `assets/` and embedded in the README |
| **0.2.0** | 🧠 Considering | Optional `--lang` flag for non-English onboarding; per-project pattern scoping |

Have a request? Open an issue — see [Contributing](#contributing) below.

---

## Contributing

Issues, ideas, and PRs are welcome. A few light guidelines:

- **Bugs**: open an issue with a minimal reproduction (which Claude Code version, what pattern, what fired).
- **Pattern detection improvements**: include a sample `~/.claude/patterns/<slug>.md` and a description of the false positive or false negative.
- **PRs**: keep them focused (one concern per PR), update the [CHANGELOG](CHANGELOG.md), and bump the version in `.claude-plugin/plugin.json` if behavior changes.

For larger design discussions, open an issue first so we don't both build the same thing in parallel.

---

## License

MIT — see [LICENSE](LICENSE).

---

## Credits

Built by [Danilo Giudice](https://github.com/cryptosecco) for people who keep typing the same five steps every morning and wish someone were paying attention.

The mushroom 🍄 is the mark. If you see it at the end of a turn, Dario has something for you.
