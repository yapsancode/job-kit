# Cross-tool portability: rules + commands conversion

Status: **not started, plan reviewed and ready to pick up.** An earlier
attempt was scoped out in conversation with the maintainer and briefly
prototyped, then fully reverted (no branch, no files left behind) so a
contributor starts from a clean `main`. This doc supersedes the original
draft of this plan — one of its assumptions turned out to be wrong (see
"Correction" below), so read this version, not old notes elsewhere.

## Goal
Let someone use job-kit with an agent other than Claude Code (OpenCode,
Cursor, GitHub Copilot, Codex CLI, Gemini CLI, etc.) without rewriting the
project. Keep the `/analyze -> /tailor -> /review -> /prep` workflow and the
two core promises (never invent, ATS-safe) identical everywhere.

## Why this is feasible (verified July 2026)
- **`AGENTS.md`** is the cross-tool standard for project instruction files,
  now under the Linux Foundation's Agentic AI Foundation (Dec 2025). Read
  natively by OpenCode, Cursor, Codex CLI, Gemini CLI, GitHub Copilot,
  Windsurf, Aider, Devin, Zed, JetBrains Junie, and more. 60,000+ GitHub
  repos already have one.
- **Agent Skills** (`SKILL.md`) is the portable, open replacement for
  tool-specific slash commands, published by Anthropic as an independent
  standard on Dec 18, 2025 and adopted within 48 hours by Microsoft and
  OpenAI. ~40 tools support it as of mid-2026, including all the ones
  listed above. A skill written once in this format runs unchanged across
  every tool that implements the standard, as long as it sticks to the
  portable core (see "Gotchas" below).
- `master-resume.tex`, the `applications/<company>/` folder convention, and
  the pdflatex/pdftotext tooling are **already tool-agnostic** — none of
  that needs to change. Only the rules file and the commands need
  converting.

## Correction to the original plan
The original draft of this doc assumed Claude Code's own AGENTS.md support
might be good enough to drop `CLAUDE.md` entirely. **Checked and false**:
as of Claude Code 2.1.x (July 2026), Claude Code does **not** read
`AGENTS.md` natively. It reads `CLAUDE.md`, and only falls back to
`AGENTS.md` when no `CLAUDE.md` exists in a directory. So the fix is not
"replace CLAUDE.md with AGENTS.md" but "make AGENTS.md canonical and have
CLAUDE.md import it" (Phase 1 below). Every other target tool (OpenCode,
Cursor, Codex, Gemini CLI, Copilot) reads AGENTS.md natively already.

## What's Claude-only today
- `CLAUDE.md` — read natively only by Claude Code.
- `.claude/commands/*.md` (`analyze`, `tailor`, `review`, `prep`, `log`) —
  Claude Code slash-command format, not read by any other tool.
- The `$ARGUMENTS` token inside each of those 5 files — a Claude Code
  substitution that is dead text in every other tool.
- Two lines inside `tailor.md` that say "Follow every rule in CLAUDE.md" /
  "Apply the design rules from CLAUDE.md" — a broken pointer for any tool
  that never loads CLAUDE.md.

## Decisions already made (Phase 0 — don't re-litigate these)
| Decision | Answer | Why |
|---|---|---|
| Canonical skills directory | **`.claude/skills/`** | Read natively by both Claude Code and OpenCode with zero extra setup. Only Copilot wants a different path (`.github/skills/`), and that's handled as an optional later phase, not by picking a compromise path nobody defaults to. |
| CLAUDE.md <-> AGENTS.md link | **`@AGENTS.md` import line inside CLAUDE.md**, not a symlink | Symlinks need Developer Mode / admin rights on Windows, which is a real barrier for contributors and forkers. An import is one plain-text line and needs no OS permissions. |
| Old `.claude/commands/` | **Delete once the new skills are verified working** (end of Phase 5), not kept alongside | The repo has no external users depending on the old format yet, and having two things both trying to be `/analyze` risks Claude Code picking the wrong one. |

If a contributor wants to challenge one of these, that's fine — just flag
it to the maintainer before diverging, since they were chosen deliberately
in a prior discussion, not defaults.

## The conversion plan

### Phase 1 — Rules -> `AGENTS.md`
1. Create `AGENTS.md` at repo root. Move the entire body of `CLAUDE.md`
   into it verbatim (ground truth, ATS formatting, writing style,
   education/work/projects distinction, file organization, language). Drop
   nothing. Generalize the first line — it currently says "Claude Code
   reads this file automatically"; make it tool-neutral since every agent
   reads this file now.
2. Replace `CLAUDE.md`'s body with:
   ```
   @AGENTS.md
   ```
   (plus a one-line note above it explaining that the rules live in
   AGENTS.md now). Claude Code still reads CLAUDE.md and pulls in
   AGENTS.md through the import; every other tool reads AGENTS.md
   directly. One source of truth, no drift between two rule files.

### Phase 2 — Commands -> Skills
Convert each file in `.claude/commands/` to `.claude/skills/<name>/SKILL.md`.
Three fixes apply per file:

**A. Add YAML frontmatter.** Commands currently have none — the name comes
from the filename and the description is implied by line 1. Skills need:
```yaml
---
name: analyze
description: Analyze a job posting and map its requirements to the master resume (strong / partial / gap). Run explicitly when the user provides a job description or a URL to one. Writes job-analysis.md; does not write the resume.
---
```
The description does double duty: human-readable doc, and the signal tools
use for auto-matching. Phrasing it as "run explicitly when..." is the
portable substitute for Claude Code's `invocation: user` field, which other
tools silently ignore — it nudges auto-invoking tools away from firing
`/tailor` or `/log` (both have side effects: they write files and compile
PDFs) during unrelated conversation.

**B. Remove `$ARGUMENTS`.** All 5 files use it. Since the instructions are
written in first person ("my message", "ask me"), no rewrite of voice is
needed elsewhere — just point at the message instead of the token, e.g.
"The job description (or a URL to it) is in my message" instead of
"...follows: $ARGUMENTS".

**C. Fix CLAUDE.md cross-references.** Only `tailor.md` has these (two
places): "Follow every rule in CLAUDE.md" and "Apply the design rules from
CLAUDE.md" both become "...in AGENTS.md" / "...from AGENTS.md".

| From | To | Fixes needed |
|---|---|---|
| `.claude/commands/analyze.md` | `.claude/skills/analyze/SKILL.md` | A, B |
| `.claude/commands/tailor.md` | `.claude/skills/tailor/SKILL.md` | A, B, **C** |
| `.claude/commands/review.md` | `.claude/skills/review/SKILL.md` | A, B |
| `.claude/commands/prep.md` | `.claude/skills/prep/SKILL.md` | A, B |
| `.claude/commands/log.md` | `.claude/skills/log/SKILL.md` | A, B |

Otherwise, keep the wording as close to the original as possible — the
content and tone were already reviewed, this is a format conversion, not a
rewrite. Do NOT add Claude-specific frontmatter extensions
(`disable-model-invocation`, `context: fork`, etc.) — they load harmlessly
in other tools but do nothing, which is a subtle way to lose portability
without any error telling you.

Once all 5 are converted and verified (Phase 5), delete
`.claude/commands/`.

### Phase 3 — Multi-tool directory strategy (optional, do last)
`.claude/skills/` already covers Claude Code + OpenCode. If Copilot support
is wanted, it needs `.github/skills/` too:
- Mac/Linux: symlink `.github/skills -> .claude/skills`.
- Windows: symlinks need Developer Mode, so ship a small cross-platform
  sync script (`scripts/sync-skills`, Node or Python, ~15 lines) that
  copies `.claude/skills/` into `.github/skills/`, and mention running it
  after editing a skill.

Don't block the rest of the migration on this — it's additive.

### Phase 4 — Docs
- `README.md`: rewrite the framing from "uses Claude Code" to "works in any
  AGENTS.md + Agent Skills agent." Add a short per-tool quick-start table.
  Keep the two core promises (never invent, ATS-safe) front and center —
  see `CONTRIBUTING.md`, they must survive any contribution.
- `CONTRIBUTING.md`: update the reference to Claude-only commands to point
  contributors at the `SKILL.md` format instead.
- This file: mark Status as done once Phase 5 passes.

### Phase 5 — Validate (do not skip)
1. Run all 5 skills end-to-end in **Claude Code** on a throwaway job
   posting. Confirm files land correctly in `applications/`, the
   never-invent rule is respected, and the resume PDF is one page.
2. Run the same end-to-end pass in **at least one non-Claude tool.**
   OpenCode is the recommended first target — it's free, open-source, and
   reads both `AGENTS.md` and `.claude/skills/` natively, so it's the
   lowest-friction proof that the conversion actually works elsewhere and
   not just in theory.
3. Only mark this plan done once both passes succeed.

### Phase 6 — Ship
Branch -> commit in logical chunks (AGENTS.md/CLAUDE.md, then the 5
skills, then docs, then the `.claude/commands/` deletion) -> PR against
`main`. `.claude/settings.local.json` is git-ignored and Claude-specific;
leave it untouched.

## Effort / risk estimate
| Phase | Effort | Risk |
|---|---|---|
| 1 — AGENTS.md + CLAUDE.md import | ~5 min | none |
| 2 — 5 skill conversions | ~30 min | low, mechanical |
| 3 — Copilot directory (optional) | ~15 min | low |
| 4 — docs | ~30 min | none |
| 5 — validate in 2 tools | ~30 min | this is where real issues surface |
| 6 — ship | ~10 min | none |

Roughly 2 hours end to end; Phases 1-2 alone (~35 min) already make the
project work in Claude Code, OpenCode, Cursor, Codex CLI, and Gemini CLI.

## Open questions for whoever picks this up
- Is Copilot support (Phase 3) wanted for v1, or can it wait for a
  follow-up PR once someone actually asks for it?
- Should the exact skills-directory paths Cursor and Codex CLI expect be
  double-checked against their current docs at implementation time, in
  case they've changed since this was last verified (July 2026)?
