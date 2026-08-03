# Project rules for this job-search workspace

These rules apply to every agent, every skill, and every generated document.

## Ground truth
- `master-resume.tex` is the ONLY source of truth about the candidate.
- NEVER invent, exaggerate, or assume jobs, titles, dates, skills, tools,
  metrics, or achievements. If information is missing, ASK the user.
- If a job requirement is not covered by the master resume, report it as a
  gap. Do not paper over it. Honest gaps are useful; fabrication is not.

## Asking the user questions
- Before asking anything, check whether `master-resume.tex` already answers it.
- Collect every question you have FIRST, then ask them together as ONE
  batched, numbered list — never one question at a time across many turns.
- If a second round is truly needed, keep it short and explain why.

## ATS-safe formatting rules (apply to every generated resume)
- Single column only. No tables, no text boxes, no images, no icons.
- Contact info in the document body, not in a page header/footer.
- Standard section names only: Summary, Skills, Work Experience,
  Projects, Education, Certifications.
- Reverse-chronological order (most recent first).
- One consistent date format, e.g. "Jan 2023 - Present".
- Consistent section styling: section title, small gap, then a full-width
  rule below it (use the titlesec package so spacing never clamps the line).
- No em dashes. Use " | " as a separator in the contact line and between a
  role title and its organisation; inside sentences, restructure or use a
  colon or hyphen.
- Keep the tailored resume to ONE page (two only if 10+ years experience).
- Compile with pdflatex. After compiling, run a text-extraction check
  (pdftotext) to confirm the text reads top-to-bottom in the correct order.

## Writing style for bullets
- Start with an action verb. Include a real number where the master resume
  provides one. Never fabricate numbers.
- Mirror the job description's exact wording ONLY when it truthfully
  describes the candidate's experience.

## Distinguishing education, work, and projects
- Bootcamps, academies, and training programmes are EDUCATION, not work
  experience — even if intensive or full-time. Place them under Education.
- Capstone or course projects belong under Projects, where strong technical
  work can still stand out. Do not duplicate the same project in two
  sections.

## File organization
Each application lives in its own folder named for the company AND the
position: `applications/<company>-<position>/`, for example
`applications/oxydata-software-agentic-ai-engineer/`. Always include the
position, even for a first application. Applying to a second role at the
same company later must never overwrite the first one.

To build a folder name: lowercase everything, replace spaces with hyphens,
and drop commas, slashes, brackets, and location tags. "Agentic AI Engineer
(MY)" at "Oxydata Software Sdn Bhd" becomes
`oxydata-software-agentic-ai-engineer`.

Inside the folder:
- `job-description.md` — the original posting text
- `job-analysis.md` — output of the analyze skill
- `resume.tex` + `cover-letter.tex` — the LaTeX sources, always under these
  exact names so every skill can find them
- the two compiled PDFs, named for the recruiter who receives them (see
  below)
- `changelog.md` — every change vs. master resume, with a one-line reason
- `review.md` — output of the review skill
- `interview-prep.md` — output of the prep skill

## Naming the compiled PDFs
The PDF filename is what a recruiter sees when they download it, so it
carries the candidate's name and the role:

    <Full-Name>-Resume-<Position>.pdf
    <Full-Name>-Cover-Letter-<Position>.pdf

for example `Jordan-Ruiz-Resume-Agentic-AI-Engineer.pdf`. Capitalise each
word and join with hyphens. Take the full name from `master-resume.tex` and
the position from `job-analysis.md`. Drop location tags and anything after a
comma from a long title, so "Senior Data Analyst, Enterprise Platform (MY)"
becomes `Senior-Data-Analyst`.

Do not rename the `.tex` files to match. Set the PDF name at compile time:

    pdflatex -jobname="Jordan-Ruiz-Resume-Agentic-AI-Engineer" resume.tex

Run it twice, as usual, so LaTeX resolves its references. If a skill needs
to read a compiled resume later, find it by pattern (`*-Resume-*.pdf`)
rather than assuming a fixed filename.

## Language
- Explain things in simple, clear language. Some users are not native
  English speakers. Avoid jargon, or explain it when unavoidable.
