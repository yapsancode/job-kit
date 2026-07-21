# Cross-tool portability: rules + commands conversion

Status: **implemented; static validation passed, runtime validation pending.**
The required harness CLIs and LaTeX tools were not installed in the
implementation environment on July 21, 2026. An earlier
attempt was scoped out in conversation with the maintainer and briefly
prototyped, then fully reverted (no branch, no files left behind) so a
contributor starts from a clean `main`. This doc supersedes the original
draft of this plan — one of its assumptions turned out to be wrong (see
"Correction" below), so read this version, not old notes elsewhere.

## Goal
Let someone use job-kit with Claude Code, OpenCode, or Codex CLI without
rewriting the project. Keep the `analyze -> tailor -> review -> prep`
workflow and the two core promises (never invent, ATS-safe) identical in all
three. Invocation syntax is tool-specific: Claude Code exposes `/analyze`,
Codex uses `$analyze`, and OpenCode selects the skill from a normal request.
Other harnesses can be added after a user validates them.

## Why this is feasible (verified July 2026)
- **`AGENTS.md`** is the cross-tool standard for project instruction files,
  now under the Linux Foundation's Agentic AI Foundation (Dec 2025). Read
  natively by OpenCode, Cursor, Codex CLI, Gemini CLI, GitHub Copilot,
  Windsurf, Aider, Devin, Zed, JetBrains Junie, and more. 60,000+ GitHub
  repos already have one.
- **Agent Skills** (`SKILL.md`) is the portable, open replacement for
  tool-specific slash commands, published by Anthropic as an independent
  standard on Dec 18, 2025 and adopted within 48 hours by Microsoft and
  OpenAI. ~40 tools support it as of mid-2026. The file format is portable,
  but discovery directories are not yet fully standardised: Claude Code
  reads `.claude/skills/`, Codex CLI reads `.agents/skills/` and
  `.codex/skills/`, and OpenCode reads `.claude/skills/` and
  `.agents/skills/`. Supporting both Claude Code and Codex therefore
  requires two checked-in discovery locations.
- `master-resume.tex`, the `applications/<company>/` folder convention, and
  the pdflatex/pdftotext tooling are **already tool-agnostic** — none of
  that needs to change. Only the rules file and the commands need
  converting.

## Correction to the original plan
The original draft of this doc assumed Claude Code's own AGENTS.md support
might be good enough to drop `CLAUDE.md` entirely. **Checked and false**:
as of Claude Code 2.1.x (July 2026), Claude Code does **not** read
`AGENTS.md` natively. It reads `CLAUDE.md`. So the fix is not
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

## Decisions for implementation (Phase 0)
| Decision | Answer | Why |
|---|---|---|
| Skills directories | **Check identical skills into `.claude/skills/` and `.agents/skills/`** | Claude Code only discovers the first; Codex CLI discovers the second; OpenCode discovers both. Five stable Markdown files are small enough that explicit duplication is safer than symlinks, wrappers, or a new build dependency. Treat `.agents/skills/` as canonical when editing and mirror each change into `.claude/skills/`. |
| CLAUDE.md <-> AGENTS.md link | **`@AGENTS.md` import line inside CLAUDE.md**, not a symlink | Symlinks need Developer Mode / admin rights on Windows, which is a real barrier for contributors and forkers. An import is one plain-text line and needs no OS permissions. |
| Old `.claude/commands/` | **Delete once the new skills are verified working** (end of Phase 5), not kept alongside | The repo has no external users depending on the old format yet, and having two things both trying to be `/analyze` risks Claude Code picking the wrong one. |

The dual-directory decision corrects the earlier `.claude/skills/`-only
plan after checking the current Codex CLI skill loader. Challenge any of
these decisions with the maintainer before implementation rather than
silently diverging.

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
Convert each file in `.claude/commands/` to
`.agents/skills/<name>/SKILL.md`, then copy the finished file unchanged to
`.claude/skills/<name>/SKILL.md`. Three fixes apply per file:

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

| From | To (in both skill roots) | Fixes needed |
|---|---|---|
| `.claude/commands/analyze.md` | `analyze/SKILL.md` | A, B |
| `.claude/commands/tailor.md` | `tailor/SKILL.md` | A, B, **C** |
| `.claude/commands/review.md` | `review/SKILL.md` | A, B |
| `.claude/commands/prep.md` | `prep/SKILL.md` | A, B |
| `.claude/commands/log.md` | `log/SKILL.md` | A, B |

Otherwise, keep the wording as close to the original as possible — the
content and tone were already reviewed, this is a format conversion, not a
rewrite. Do NOT add Claude-specific frontmatter extensions
(`disable-model-invocation`, `context: fork`, etc.) — they load harmlessly
in other tools but do nothing, which is a subtle way to lose portability
without any error telling you.

Once all 5 are converted and verified (Phase 5), delete
`.claude/commands/`.

### Phase 3 — Verify mirrored skills
Compare `.agents/skills/` and `.claude/skills/` recursively and fail the
validation if any file differs. Do not add a sync script or build dependency
for five stable Markdown files. If maintaining the copies becomes a real
problem, automate it in a follow-up.

Copilot's `.github/skills/` directory is out of scope for this first pass.
Add it only when a Copilot user requests and can validate it.

### Phase 4 — Docs
- `README.md`: rewrite the framing from "uses Claude Code" to name the three
  validated tools explicitly. Add a short per-tool quick-start table and
  show the correct invocation syntax (`/analyze` in Claude Code,
  `$analyze` in Codex, and a normal "use the analyze skill" request in
  OpenCode). Avoid claiming support for tools that were not tested.
  Keep the two core promises (never invent, ATS-safe) front and center —
  see `CONTRIBUTING.md`, they must survive any contribution.
- `CONTRIBUTING.md`: update the reference to Claude-only commands to point
  contributors at the `SKILL.md` format instead.
- This file: mark Status as done once Phase 5 passes.

### Phase 5 — Validate (do not skip)
1. Run all 5 skills end-to-end in **Claude Code** on a throwaway job
   posting. Confirm files land correctly in `applications/`, the
   never-invent rule is respected, and the resume PDF is one page.
2. Run the same end-to-end pass in **OpenCode**. It reads `AGENTS.md` and
   both skill directories, making it the lowest-friction full portability
   test. Request each named skill normally and confirm OpenCode loads it
   through the native `skill` tool.
3. In **Codex CLI**, confirm all five skills are discovered from
   `.agents/skills/` and invoke at least `$analyze` through the point where
   it writes `job-description.md` and `job-analysis.md`. A full PDF pass is
   optional because the same skill files already passed in OpenCode.
4. Compare `.agents/skills/` and `.claude/skills/` recursively; they must be
   byte-for-byte identical.
5. Only mark this plan done once all checks pass.

Current validation status (July 21, 2026):

| Check | Status | Evidence |
|---|---|---|
| Static skill structure | Passed | Five valid `SKILL.md` files exist in each discovery root. |
| Mirror equality | Passed | `diff -rq .agents/skills .claude/skills` produced no differences. |
| Claude Code runtime | Not installed | `claude` was not available on `PATH`. |
| Codex CLI runtime | Not installed | `codex` was not available on `PATH`. |
| OpenCode runtime | Not installed | `opencode` was not available on `PATH`. |
| PDF workflow | Not installed | Neither `pdflatex` nor `pdftotext` was available on `PATH`. |

The implementation is ready for review, but Phase 5 remains open until the
three runtime checks are completed in environments with those tools.

### Phase 6 — Ship
Branch -> commit in logical chunks (AGENTS.md/CLAUDE.md, then the 5
skills, then docs, then the `.claude/commands/` deletion) -> PR against
`main`. `.claude/settings.local.json` is git-ignored and Claude-specific;
leave it untouched.

## Effort / risk estimate
| Phase | Effort | Risk |
|---|---|---|
| 1 — AGENTS.md + CLAUDE.md import | ~5 min | none |
| 2 — 5 skill conversions in 2 roots | ~40 min | low, mechanical |
| 3 — Verify mirrored skills | ~5 min | low |
| 4 — docs | ~30 min | none |
| 5 — validate in 3 tools | ~30 min | this is where real issues surface |
| 6 — ship | ~10 min | none |

Roughly 2 hours end to end. This pass claims support only for Claude Code,
OpenCode, and Codex CLI because those are the tools explicitly validated.

## Deferred scope
Future tools should get another checked-in skill mirror only after a user
volunteers to validate that harness.
