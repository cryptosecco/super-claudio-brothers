# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-04-24

### Added
- Initial release of `super-claudio-brothers` plugin.
- `skills/super-claudio-brothers/SKILL.md` — full skill definition (~260 lines, 15 tested scenarios).
- `hooks/hooks.json` — `SessionStart` hook that auto-loads `~/.claude/patterns/PATTERNS.md` into every session.
- `.claude-plugin/plugin.json` + `marketplace.json` — plugin manifests for `/plugin install` route.
- README with install, behavior, file layout, controls, troubleshooting.

### Known gaps
- Demo GIF and static proposal-block screenshot pending — will ship in 0.1.1.
- No automated tests in CI — skill is behavior-tested via subagent scenarios documented in `SKILL.md`.
