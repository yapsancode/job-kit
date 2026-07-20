# Future planning: cross-tool portability

Status: not started. This is a plan, not implemented yet.

## Goal
Let someone use job-kit with a tool other than Claude Code (Cursor, GitHub
Copilot Agent Mode, Gemini CLI, Codex CLI, JetBrains Junie, etc.) without
rewriting the project.

## Why this is feasible
- `AGENTS.md` is now the cross-tool standard for project instruction files
  (originated by OpenAI, now under the Linux Foundation's Agentic AI
  Foundation). Natively supported by 28+ tools as of mid-2026.
- The **Agent Skills** open standard (`SKILL.md`) is the portable
  replacement for tool-specific slash commands. Adopted by 30+ tools
  including Cursor, Copilot Agent Mode, Gemini CLI, Codex CLI, and
  JetBrains Junie as of mid-2026. A skill written once in this format runs
  unchanged across those tools.
- `master-resume.tex`, the `applications/` folder convention, and the LaTeX
  tooling are already tool-agnostic — no changes needed there.

## What's Claude-only today
- `CLAUDE.md` — read natively only by Claude Code.
- `.claude/commands/*.md` (`analyze`, `tailor`, `review`, `prep`, `log`) —
  plain Claude Code slash commands, not portable.

## Proposed migration
1. Add `AGENTS.md` at the repo root, containing the ground-truth and
   ATS-formatting rules currently in `CLAUDE.md`. Keep `CLAUDE.md` as a
   thin pointer to `AGENTS.md` (or drop it if Claude Code's `AGENTS.md`
   support is confirmed sufficient).
2. Convert each command under `.claude/commands/` into a
   `skills/<name>/SKILL.md` following the Agent Skills spec:
   - `skills/analyze/SKILL.md`
   - `skills/tailor/SKILL.md`
   - `skills/review/SKILL.md`
   - `skills/prep/SKILL.md`
   - `skills/log/SKILL.md`
3. Verify behavior in at least one non-Claude tool (e.g. Cursor or Gemini
   CLI) before calling the migration done.
4. Update `README.md` to document both invocation styles (Claude Code slash
   commands vs. portable skills) if both are kept side by side.

## Open questions
- Keep `.claude/commands/` around for backward compatibility, or fully
  replace with `skills/`?
- Does Claude Code's own `AGENTS.md` support match `CLAUDE.md` closely
  enough to drop `CLAUDE.md` entirely?
