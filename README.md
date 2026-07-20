# Resume Tailor

Turn one master resume into a job-specific, ATS-safe resume and cover letter
in minutes — using [Claude Code](https://claude.com/claude-code) and LaTeX.

You keep **one** master resume with everything you have ever done. For each
job, you paste the posting and run a few commands. Claude reads the job,
picks your most relevant experience, and generates a tailored one-page
resume and cover letter as PDFs — then stress-tests them like a real
recruiter and an ATS robot.

## Why this exists

Applying to jobs one by one is slow, and most people either send the same
generic resume everywhere or spend an hour rewriting it per job. This kit
makes each tailored application take about 15 minutes, while keeping two
rules that actually matter:

1. **Never invent anything.** Claude is instructed to never fabricate
   experience, skills, or numbers. Gaps are reported honestly, not papered
   over. What you send is something you can defend in an interview.
2. **Pass the robots, please the humans.** The template is ATS-safe (single
   column, standard headings, no tables or graphics) *and* clean-looking for
   the human who reads it after the software filter.

## How it works

```
/log      keep your master resume fresh as you ship and learn (use anytime)
   |
/analyze  paste a job posting -> match report (strong / partial / gap)
   |
/tailor   generate the tailored resume + cover letter as PDFs
   |
/review   stress-test: skeptical recruiter + ATS robot, scored out of 10
   |
/prep     before the interview: likely questions, STAR answers, mock round
```

## Requirements

- A [Claude](https://claude.ai) Pro or Max subscription (Claude Code is included)
- [Claude Code](https://code.claude.com/docs/en/setup) installed
- A LaTeX distribution (for compiling PDFs):
  - Windows: [MiKTeX](https://miktex.org)
  - macOS: [MacTeX](https://tug.org/mactex/) (or `brew install --cask mactex-no-gui`)
  - Linux: `sudo apt install texlive-latex-recommended texlive-latex-extra`

## Quick start

```bash
git clone https://github.com/YOUR-USERNAME/resume-tailor.git
cd resume-tailor
claude
```

Then, inside Claude Code:

1. **Build your master resume.** Copy your current resume (PDF or Word) into
   this folder, then tell Claude:
   > Read my resume file and expand it into master-resume.tex. Then interview
   > me one question at a time to fill in missing numbers and details.

   Your master resume should hold *more* than a normal resume — every
   project, every number, every skill. It can be several pages; it is never
   sent anywhere. See `master-resume.tex` for the template and format.

2. **Apply to a job.** Find a real posting, copy the text, then:
   ```
   /analyze  [paste the job description]
   /tailor   [company name]
   /review   [company name]
   ```
   Your PDFs land in `applications/<company>/`.

3. **Before the interview:**
   ```
   /prep [company name]
   ```

4. **Keep it fresh.** Whenever you ship or learn something:
   ```
   /log finished eval harness for my project, accuracy went from 70% to 91%
   ```

## The commands

| Command    | What it does |
|------------|--------------|
| `/analyze` | Studies a job posting and maps each requirement to your experience: strong match, partial match, or honest gap. Writes nothing yet. |
| `/tailor`  | Builds the tailored one-page resume + cover letter as PDFs, following the ATS-safe rules. Shows a changelog and flags any gap it refused to fake. |
| `/review`  | Two strict reviewers: a recruiter doing a 10-second scan, and an ATS robot checking keyword coverage and parse order. Scores each out of 10. |
| `/prep`    | Generates likely interview questions, STAR answers built from your real experience, honest ways to handle gaps, and smart questions to ask them. Can run a mock interview. |
| `/log`     | Files a new achievement or number into your master resume as a proper bullet, keeping your facts exact. |

The commands are just readable Markdown files in `.claude/commands/`. Open
them, edit them, make them yours.

## The rules Claude follows

Every session, Claude Code reads `CLAUDE.md` — the project rules. In short:
`master-resume.tex` is the only source of truth, nothing is ever invented,
resumes stay ATS-safe and one page, and everything is organised per company
under `applications/`. Read and adjust `CLAUDE.md` to fit your own standards.

## A note on honesty

This tool is deliberately built to *not* lie for you. If a job needs a skill
you do not have, `/analyze` and `/review` will tell you plainly. That is the
point: a tailored resume gets you the interview, but only truthful content
gets you through it. Use the gaps as a study list, not something to hide.

## Contributing

Issues and pull requests welcome — better LaTeX templates, new commands
(e.g. LinkedIn outreach, skill-gap planning), and language improvements
especially. Keep the two core rules intact: ATS-safe, and never invent.

## License

MIT — see [LICENSE](LICENSE). Free to use, fork, and share.
