---
name: super-claudio-brothers
description: Auto-detects multi-step workflows the user has repeated 3+ times within or across sessions and proposes them as reusable skills at the 4th occurrence — runs automatically, no manual invocation needed. Internal triggers to watch for: familiar sequences executed on autopilot, déjà vu on a multi-step ritual, user cues like "same drill" / "again" / "like last time". Not for single-step commands (hooks/aliases) or style preferences (CLAUDE.md).
---

# Super Claudio Brothers

## Overview

Claude frequently executes the same multi-step ritual across sessions without noticing. This is **perceptual blindness**: there is no natural mechanism to aggregate "I have done this before." This skill closes the gap by tracking recurring workflows in `~/.claude/patterns/` and proposing skill crystallization at the 4th occurrence.

**Core principle:** crystallize workflows the user cannot see themselves, not preferences they have already stated.

## First-run onboarding

### Pre-flight checks (run at the start of every turn where this skill activates)

Check these two flag files in `~/.claude/patterns/`:

1. **If `.disabled` exists** — the user has turned the skill off. Do nothing: no tracking, no suggestions, no onboarding. If the user explicitly asks to re-enable, delete the file.
2. **If `.onboarded` does NOT exist** — this is the user's first activation. Run the onboarding flow below **before** any tracking or detection logic.
3. **Otherwise** — proceed to Detection protocol.

### Onboarding flow (first activation only)

Present this welcome to the user (adapt to the user's apparent language — they may be working in Italian, Spanish, etc.):

```
🍄 Super Claudio Brothers — first run

Two brothers will now watch your workflow:
  • Claudio — the Watcher. Observes patterns silently, writes to ~/.claude/patterns/
  • Dario   — the Proposer. At the 4th repetition, offers a ready-made skill

I watch for multi-step workflows you repeat 3+ times (within a
session or across sessions) and propose crystallizing them as
reusable skills.

How it works:
  • 1st–2nd time you do something: I stay silent.
  • 3rd time:  I silently start tracking in ~/.claude/patterns/.
  • 4th time:  after finishing the task, I propose a new skill
               named itsme-<slug> that you can approve or decline.

I will NOT skillify:
  • Language/style preferences    → CLAUDE.md
  • Single-command habits         → shell aliases or hooks
  • Project-specific rituals      → project CLAUDE.md

Files I touch:
  • ~/.claude/patterns/PATTERNS.md        (index)
  • ~/.claude/patterns/<slug>.md          (one per tracked pattern)
  • ~/.claude/skills/itsme-<slug>/        (only after your approval)

Controls:
  • Declining is permanent per pattern (re-enable by editing the
    file's status back to "tracking").
  • Disable entirely: tell me "disable super claudio brothers"
    or create ~/.claude/patterns/.disabled.

Optional upgrade: I can wire a SessionStart hook via /update-config
so cross-session counts are always loaded. Without it coverage is
~80%.

Reply "ok" to proceed, "disable" to turn me off, or "install hook"
for the upgrade.
```

### Onboarding completion

Act on the user's reply:

| Reply | Action |
|-------|--------|
| "ok", a simple continuation of work, or any non-refusal | Write `~/.claude/patterns/.onboarded` with today's date. Proceed to Detection protocol. |
| "disable" | Write both `.onboarded` AND `.disabled`. Skill goes silent until the user deletes `.disabled`. |
| "install hook" | Invoke `/update-config` to add the `SessionStart` hook (snippet in the Installation section below). Then write `.onboarded` and proceed. |

Do NOT re-run onboarding once `.onboarded` exists — strictly one-time.

## Detection protocol

### Step 0 — Load tracked patterns at session start

*Assumes pre-flight checks (see First-run onboarding) have passed: `.onboarded` exists and `.disabled` does not.*

If this is the first turn of a session and `~/.claude/patterns/PATTERNS.md` exists, **read it immediately** (`cat ~/.claude/patterns/PATTERNS.md`) so tracked patterns are in working context for the whole session. Do this before responding to the user's first substantive message. This is critical in base installation mode (no `SessionStart` hook) — without it, cross-session counts are invisible.

### Step 1 — Per-turn check

At every turn, before executing a multi-step task, run this internal check:

1. Am I about to execute a sequence I have done before (this session or past sessions)?
2. Does it have **3+ distinct steps** OR non-trivial decision logic?
3. Is `~/.claude/patterns/` already tracking it?

If yes to (1) and (2): log or increment. If count reaches 4: fire the soft-blocking suggestion at end of turn.

### Fuzzy matching

A new observation matches a tracked pattern if ALL of these hold:

- **High-level flow** is the same (same abstract shape and intent)
- **Step count and order** are the same
- **Goal category** is the same. Reference taxonomy: `bug-fix`, `refactor-design`, `audit`, `migration`, `performance-triage`, `review`, `investigation`, `scaffold`. **If goal category differs, split** even when 50% of steps overlap — a bug-fix and a refactor-design that happen to share "git log + read files" are different patterns.

Specific details DO NOT need to match — filenames, keywords, error strings, subsystems, or tools invoked can all differ. Match on the *shape* of the workflow, not its surface.

If in doubt whether two workflows are "the same pattern", they probably are — track them together under the same entry and refine the description as needed.

### Drift handling

When a new observation matches a tracked pattern on N-1 steps but one step has shifted (e.g., the tracked pattern ends with "verify tests" but this occurrence ends with "benchmark performance"):

- **If the drifting step serves the same abstract goal** (both "verify tests" and "benchmark perf" are forms of *verification*): **merge**. Increment count on the existing pattern. Generalize the step description in the pattern file to cover both variants (e.g., `verify — tests or perf benchmark`). Log the variant in `## Observations`.
- **If the drifting step changes the goal category** (e.g., the final step flips the workflow from `refactor-design` to `migration`): **split**. Create a new pattern file. Leave the existing pattern unchanged at its current count.
- **When unsure, merge and refine.** Under-merging produces fragmented patterns and misses crystallization opportunities; over-merging is recoverable later by splitting.

## Thresholds

| Occurrence | Action |
|------------|--------|
| 1st | Ignore — normal activity |
| 2nd | Note internally, do not log |
| 3rd | **Start tracking** — write `~/.claude/patterns/<slug>.md` with `status: tracking`, `count: 3`, and add 1-line entry to `PATTERNS.md` |
| 4th | **Soft-blocking suggestion** — complete the user's current task, then present the proposal prominently at end of turn |
| User declines | Set `status: declined` **permanently**. Keep file as audit trail. NEVER re-propose this pattern, even if count keeps incrementing. Only the user can re-enable it by editing `status` back to `tracking` |
| User approves | Create `~/.claude/skills/itsme-<slug>/SKILL.md`, set `status: skill-created` |

## Pattern height filter (what qualifies)

Skillify **only** if ALL of the following are true:

- 3+ distinct steps OR non-trivial decision logic
- Generalizable across projects (not tied to one repo's specifics)
- Not already covered by an existing skill — verify with `ls ~/.claude/skills/`
- Not reducible to a hook, alias, or CLAUDE.md rule
- **Not a default LLM capability** — generic cognitive tasks (explain a topic, summarize, compare options, list pros/cons, translate, rewrite) are NOT skill-worthy even when their internal substeps repeat across turns. Skillify only workflows that require specific tools, file layouts, domain conventions, or decisions the model would not reconstruct unaided. Rule of thumb: if a fresh Claude instance with no context could do it equally well, it is not a skill.

### Route elsewhere instead of skillifying

| Pattern shape | Correct destination |
|---------------|---------------------|
| Single command repeated | Shell alias, or a hook via `/update-config` |
| Language / formatting preference | `~/.claude/CLAUDE.md` |
| Project-only ritual | Project's `CLAUDE.md` |
| Single-correction feedback | Auto-memory feedback entry |

> *"Skills earn their keep when a workflow is observed repeating; a single successful run is not enough signal. Offering a skill otherwise feels like upselling rather than helping."*

## Logging schema

### Directory layout

```
~/.claude/patterns/
  PATTERNS.md              # 1-line index per tracked pattern
  <pattern-slug>.md        # detail per pattern
```

### `PATTERNS.md` entry format

```
- [<slug>](<slug>.md) — brief description. Count: N. Status: tracking|suggested|declined|skill-created
```

### Individual pattern file format

```markdown
---
name: <slug>
description: one-line description of the workflow
count: N
status: tracking | suggested | declined | skill-created
first_seen: YYYY-MM-DD
last_seen: YYYY-MM-DD
---

## Steps
1. ...
2. ...
3. ...

## Observations
- YYYY-MM-DD: brief context of occurrence

## Proposed skill (draft at 4th hit)
- Name: itsme-<slug>
- Description: Use when <trigger>
- Type: discipline | technique | pattern | reference
```

## Suggestion template (4th hit — soft-blocking)

After completing the user's current request, append this block as the dominant final element of the turn. Do NOT bury it in body text.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🍄 PATTERN DETECTED (4th occurrence)

Pattern: <one-line description>
Observed: <first-date> → <last-date>, 4 occurrences

Proposed skill:
  • Name:        itsme-<slug>
  • Description: "Use when <trigger>"
  • Type:        <discipline | technique | pattern | reference>
  • Location:    ~/.claude/skills/itsme-<slug>/SKILL.md

Create now (invokes /superpowers:writing-skills) or decline?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**The single 🍄 marks every skill-crystallization moment.** This is Dario (the Proposer) speaking. One mushroom per proposal — no parade, no fanfare.

## Installation

### Via plugin (recommended)

```bash
/plugin marketplace add cryptosecco/super-claudio-brothers
/plugin install super-claudio-brothers@super-claudio-brothers
```

Both the skill and the `SessionStart` hook ship together — no manual `settings.json` edits needed, 100% cross-session coverage out of the box.

### Manual install (fallback)

1. Copy this folder to `~/.claude/skills/super-claudio-brothers/`.
2. Add the following snippet to `~/.claude/settings.json` (or invoke `/update-config`) for full cross-session coverage:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          { "type": "command", "command": "cat ~/.claude/patterns/PATTERNS.md 2>/dev/null || true" }
        ]
      }
    ]
  }
}
```

Without the hook the skill still works, just slightly less reliably for cross-session detection (~80% coverage).

## Red flags — STOP and check `~/.claude/patterns/`

- "I just ran this on autopilot" — you should have noticed at step 2
- "This feels familiar" — check `patterns/` NOW
- User says "same drill" / "again" / "like last time" — they are signaling, log it
- About to suggest a skill for a single preference — reroute to CLAUDE.md
- About to skillify after fewer than 4 confirmed observations — wait

## Common mistakes

| Mistake | Fix |
|---------|-----|
| Skillifying language or style preferences | Route to `CLAUDE.md` |
| Skillifying one-off complex tasks | Wait for 4 confirmed observations |
| Creating a duplicate of an existing skill | `ls ~/.claude/skills/` first |
| Storing project-only rituals globally | Put in project `CLAUDE.md` instead |
| Creating a skill silently without user approval | Always soft-block at 4th hit and ask |
| Re-proposing a pattern the user previously declined | Once declined, it is permanently declined. Increment count silently if you want (for record-keeping), but NEVER re-fire the suggestion. Only the user can re-enable by editing `status` back to `tracking` |
| Splitting near-identical workflows into separate patterns | Use fuzzy matching — if shape, step count, and goal match, it is the same pattern (details can differ) |

## Rationale for the 4-hit threshold

- **1st**: too early, indistinguishable from one-off work
- **2nd**: could still be coincidence
- **3rd**: now a pattern — start tracking, but suggesting would be premature upselling
- **4th**: pattern is confirmed; skillifying saves effort from this point forward

This matches the human intuition "three is a pattern, four is a habit."
