# Example application folder

When you run `/analyze`, `/tailor`, and `/review` for a real job, a folder
like this is created automatically at `applications/<company>-<position>/`,
for example `applications/oxydata-software-agentic-ai-engineer/`, and
filled with:

- `job-description.md` — the original posting you pasted
- `job-analysis.md` — the requirement-by-requirement match report
- `resume.tex` and `cover-letter.tex` — the LaTeX sources
- `Jordan-Ruiz-Resume-Agentic-AI-Engineer.pdf` — your tailored, ATS-safe
  one-page resume
- `Jordan-Ruiz-Cover-Letter-Agentic-AI-Engineer.pdf` — a short tailored
  cover letter
- `changelog.md` — every change made vs. your master resume, with reasons
- `review.md` — the recruiter + ATS scores and fix list
- `interview-prep.md` — likely questions and STAR answers (from `/prep`)

Two naming details are deliberate. The folder includes the **position**, so
applying to a second role at the same company months later does not
overwrite this one. The PDFs carry **your name and the role**, because that
filename is what a recruiter sees in their downloads folder after you upload
it. The `.tex` sources keep their simple names so the skills always find
them.

This EXAMPLE folder is just a placeholder so the structure is visible.
Your real application folders are git-ignored by default (see `.gitignore`)
so your job search stays private.
