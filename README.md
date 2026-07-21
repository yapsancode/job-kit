# Job Kit - A Resume Tailor

Turn one master resume into a job-specific, ATS-safe resume and cover letter
in minutes using a target-compatible AI agent harness and LaTeX.

You keep **one** master resume with everything you have ever done. For each
job, you paste the posting and run a few skills. Your agent reads the job,
picks your most relevant experience, and generates a tailored one-page
resume and cover letter as PDFs — then stress-tests them like a real
recruiter and an ATS robot.

## Why this exists

Applying to jobs one by one is slow, and most people either send the same
generic resume everywhere or spend an hour rewriting it per job. This kit
makes each tailored application take about 15 minutes, while keeping two
rules that actually matter:

1. **Never invent anything.** The agent is instructed to never fabricate
   experience, skills, or numbers. Gaps are reported honestly, not papered
   over. What you send is something you can defend in an interview.
2. **Pass the robots, please the humans.** The template is ATS-safe (single
   column, standard headings, no tables or graphics) *and* clean-looking for
   the human who reads it after the software filter.

## How it works

```
log      keep your master resume fresh as you ship and learn (use anytime)
  |
analyze  paste a job posting -> match report (strong / partial / gap)
  |
tailor   generate the tailored resume + cover letter as PDFs
  |
review   stress-test: skeptical recruiter + ATS robot, scored out of 10
  |
prep     before the interview: likely questions, STAR answers, mock round
```

## Requirements

- One target-compatible agent harness and its normal account or API access:
  [Claude Code](https://code.claude.com/docs/en/setup),
  [Codex CLI](https://developers.openai.com/codex/cli/), or
  [OpenCode](https://opencode.ai/docs/)
- A LaTeX distribution (for compiling PDFs):
  - Windows: [MiKTeX](https://miktex.org)
  - macOS: [MacTeX](https://tug.org/mactex/) (or `brew install --cask mactex-no-gui`)
  - Linux: `sudo apt install texlive-latex-recommended texlive-latex-extra`

> **Validation status (July 21, 2026):** Codex CLI 0.144.5 and OpenCode 1.18.4
> discovered all five skills, and Codex loaded `analyze` explicitly. OpenCode
> execution was blocked by the configured provider's spending limit.
> End-to-end workflow and Claude Code runtime are pending; LaTeX is unavailable
> in this environment. The legacy
> `.claude/commands/` files remain temporarily until Claude skill runtime
> validation passes.

## Quick start

```bash
git clone https://github.com/yapsancode/job-kit.git
cd job-kit
```

Start your chosen agent and invoke skills using its native syntax:

| Tool | Start | Analyze a job |
|---|---|---|
| Claude Code | `claude` | `/analyze [paste the job description]` |
| Codex CLI | `codex` | `$analyze [paste the job description]` or choose it from `/skills` |
| OpenCode | `opencode` | `Use the analyze skill for this job description: ...` |

Then, inside the agent:

1. **Build your master resume.** Copy your current resume (PDF or Word) into
   this folder, then tell the agent:
   > Read my resume file and expand it into master-resume.tex. Then interview
   > me one question at a time to fill in missing numbers and details.

   Your master resume should hold *more* than a normal resume — every
   project, every number, every skill. It can be several pages; it is never
   sent anywhere. See `master-resume.tex` for the template and format.

2. **Apply to a job.** Find a real posting, copy the text, then invoke the
   `analyze`, `tailor`, and `review` skills in that order. Use `/skill-name`
   in Claude Code, `$skill-name` in Codex, or ask OpenCode to use the named
   skill.
   Your PDFs land in `applications/<company>/`.

3. **Before the interview:**
   Invoke the `prep` skill with the company name.

4. **Keep it fresh.** Whenever you ship or learn something:
   Invoke the `log` skill with the factual update, for example: "finished
   an eval harness for my project; accuracy went from 70% to 91%."

## The skills

| Skill | What it does |
|---|---|
| `analyze` | Studies a job posting and maps each requirement to your experience: strong match, partial match, or honest gap. |
| `tailor` | Builds the tailored one-page resume and cover letter as PDFs, following the ATS-safe rules. Shows a changelog and flags any gap it refused to fake. |
| `review` | Two strict reviewers: a recruiter doing a 10-second scan, and an ATS robot checking keyword coverage and parse order. Scores each out of 10. |
| `prep` | Generates likely interview questions, STAR answers built from your real experience, honest ways to handle gaps, and smart questions to ask them. Can run a mock interview. |
| `log` | Files a new achievement or number into your master resume as a proper bullet, keeping your facts exact. |

The skills are readable Markdown files mirrored in `.agents/skills/` and
`.claude/skills/`. Keep both copies identical when customizing them.

## The rules agents follow

Every target-compatible agent reads the shared project rules from `AGENTS.md`;
`CLAUDE.md` imports that file for Claude Code compatibility. In short,
`master-resume.tex` is the only source of truth, nothing is ever invented,
resumes stay ATS-safe and one page, and everything is organised per company
under `applications/`. Adjust `AGENTS.md` to fit your own standards.

## A note on honesty

This tool is deliberately built to *not* lie for you. If a job needs a skill
you do not have, the `analyze` and `review` skills will tell you plainly.
That is the point: a tailored resume gets you the interview, but only
truthful content gets you through it. Use the gaps as a study list, not
something to hide.

## Contributing

Issues and pull requests welcome — better LaTeX templates, new skills
(e.g. LinkedIn outreach, skill-gap planning), and language improvements
especially. Keep the two core rules intact: ATS-safe, and never invent.

## License

MIT — see [LICENSE](LICENSE). Free to use, fork, and share.
