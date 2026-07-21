# Cross-Tool Portability Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make Job Kit's rules and five workflows natively discoverable in Claude Code, Codex CLI, and OpenCode.

**Architecture:** `AGENTS.md` becomes the canonical rules file, while `CLAUDE.md` imports it. Each workflow is stored byte-for-byte identically in `.agents/skills/` and `.claude/skills/`; no wrappers, symlinks, generators, or dependencies are added.

**Tech Stack:** Markdown, Agent Skills `SKILL.md`, `AGENTS.md`, LaTeX, POSIX validation commands.

**Execution status (July 21, 2026):** Tasks 1-3 completed. Task 4 is
**deferred** until Phase 5 Claude Code runtime validation passes. Static
validation, mirror equality, and Codex CLI 0.144.5 / OpenCode 1.18.4 skill
discovery passed; Codex also loaded `analyze` in a read-only invocation.
OpenCode execution was blocked by the configured provider's spending limit.
End-to-end workflow, Claude Code runtime, and PDF tooling remain pending.
Claude Code and the LaTeX tools were not installed in the implementation
environment.

---

### Task 1: Canonical Project Rules

**Files:**
- Create: `AGENTS.md`
- Modify: `CLAUDE.md`

- [ ] Copy the existing `CLAUDE.md` rules into `AGENTS.md`, changing only its opening paragraph to tool-neutral wording. Preserve every safety, formatting, writing, organization, and language rule.
- [ ] Replace `CLAUDE.md` with a compatibility note followed by `@AGENTS.md`.
- [ ] Verify the source-of-truth, never-invent, ATS, and import lines with `grep -q` checks.
- [ ] Commit with `git commit -m "Add portable project instructions"`.

The complete replacement for `CLAUDE.md` is:

```markdown
# Claude Code compatibility

Project rules are maintained in AGENTS.md.

@AGENTS.md
```

### Task 2: Portable Agent Skills

**Files:**
- Create: `.agents/skills/{analyze,tailor,review,prep,log}/SKILL.md`
- Create: `.claude/skills/{analyze,tailor,review,prep,log}/SKILL.md`
- Source: `.claude/commands/{analyze,tailor,review,prep,log}.md`

- [ ] Create each canonical skill under `.agents/skills/` with YAML `name` and `description` frontmatter.
- [ ] Replace `$ARGUMENTS` with references to the user's message, replace `CLAUDE.md` references with `AGENTS.md`, and remove slash-specific wording. Preserve every other workflow instruction.
- [ ] Copy each finished skill unchanged into `.claude/skills/` without symlinks.
- [ ] Run the checks below and confirm status 0 with no output.
- [ ] Commit with `git commit -m "Add portable job workflow skills"`.

Use these exact descriptions:

| Skill | Description |
|---|---|
| `analyze` | `Analyze a job posting and map its requirements to the master resume as strong matches, partial matches, or honest gaps. Use when the user provides a job description or URL and wants a fit analysis before tailoring.` |
| `tailor` | `Create an ATS-safe tailored resume and cover letter from an existing job analysis and the master resume. Use when the user asks to tailor an application for a company.` |
| `review` | `Review a tailored application as a skeptical recruiter and an ATS parser. Use when the user wants strict feedback on an existing resume and cover letter.` |
| `prep` | `Create interview preparation from a job analysis, tailored resume, and master resume. Use when the user wants likely questions, truthful STAR answers, and ways to discuss gaps.` |
| `log` | `Add a real achievement, measurement, skill, or project update to the master resume. Use when the user wants to record new factual experience.` |

```bash
diff -rq .agents/skills .claude/skills
grep -R '\$ARGUMENTS\|CLAUDE.md' .agents/skills .claude/skills && exit 1 || true
for skill in analyze tailor review prep log; do grep -q "^name: $skill$" ".agents/skills/$skill/SKILL.md" || exit 1; done
```

### Task 3: Cross-Tool Documentation

**Files:**
- Modify: `README.md`
- Modify: `CONTRIBUTING.md`

- [ ] Describe Job Kit as an agent-powered resume workflow targeting Claude Code, Codex CLI, and OpenCode compatibility.
- [ ] Require one target-compatible agent harness plus LaTeX instead of requiring Claude specifically.
- [ ] Document `claude` with `/analyze`, `codex` with `$analyze` or `/skills`, and `opencode` with a normal request to use the analyze skill.
- [ ] Explain that skills are mirrored in `.agents/skills/` and `.claude/skills/`, while shared rules live in `AGENTS.md`.
- [ ] Change contribution guidance from new commands to new skills and require identical edits in both skill roots.
- [ ] Verify all three tool names, `$analyze`, and both skill paths are present with `grep -q`.
- [ ] Commit with `git commit -m "Document cross-tool workflows"`.

### Task 4: Defer Legacy Command Removal

**Files:**
- Keep: `.claude/commands/analyze.md`
- Keep: `.claude/commands/tailor.md`
- Keep: `.claude/commands/review.md`
- Keep: `.claude/commands/prep.md`
- Keep: `.claude/commands/log.md`

- [ ] **Defer** deletion until Phase 5 confirms Claude Code skill runtime works.
- [ ] Until then, keep the five legacy command files unchanged so Claude Code users still have a verified invocation path.
- [ ] When Phase 5 passes, delete the directory and verify `test ! -d .claude/commands` and that skill directories contain no `$ARGUMENTS` references.

### Task 5: Final Static and Harness Validation

**Files:**
- Modify: `planning/cross-tool-portability.md`
- Modify: `planning/cross-tool-portability-implementation.md`

- [ ] Run `diff -rq` on the skill roots, count exactly five `SKILL.md` files in each root, validate each skill frontmatter, and run `git diff --check`.
- [ ] Check `claude`, `codex`, `opencode`, `pdflatex`, and `pdftotext` with `command -v`.
- [ ] For every installed and authenticated harness, validate skill discovery and invoke `analyze` on a throwaway posting. Keep throwaway output untracked and remove it afterward.
- [ ] Mark each harness `passed`, `blocked`, or `not installed` in the portability spec with an exact reason. Never claim an unexecuted runtime check passed.
- [ ] Commit the plan and validation notes with `git commit -m "Record portability validation"`.
- [ ] Run `git status --short`, `git diff main...HEAD --check`, and `git log --oneline main..HEAD`; expect a clean tree, no diff errors, and logical commits.

Static validation commands:

```bash
diff -rq .agents/skills .claude/skills
test "$(find .agents/skills -name SKILL.md | wc -l | tr -d ' ')" = 5
test "$(find .claude/skills -name SKILL.md | wc -l | tr -d ' ')" = 5
ruby -e '
  require "yaml"
  Dir.glob(".agents/skills/*/SKILL.md").each do |f|
    skill = File.basename(File.dirname(f))
    raise "no frontmatter in #{f}" unless File.read(f) =~ /\A---\n(.*?)\n---\n?/m
    meta = YAML.safe_load($1, permitted_classes: [], permitted_symbols: [], aliases: false)
    raise "bad frontmatter in #{f}" unless meta.is_a?(Hash) && meta["name"] == skill && meta["description"].is_a?(String) && !meta["description"].empty?
  end
'
grep -R '\$ARGUMENTS\|\.claude/commands' .agents/skills .claude/skills && exit 1 || true
git diff --check
```
