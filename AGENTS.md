# Project rules for this job-search workspace

These rules apply to every agent, every skill, and every generated document.

## Ground truth
- `master-resume.tex` is the ONLY source of truth about the candidate.
- NEVER invent, exaggerate, or assume jobs, titles, dates, skills, tools,
  metrics, or achievements. If information is missing, ASK the user.
- If a job requirement is not covered by the master resume, report it as a
  gap. Do not paper over it. Honest gaps are useful; fabrication is not.

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
Each application lives in `applications/<company-name>/`:
- `job-description.md` — the original posting text
- `job-analysis.md` — output of the analyze skill
- `resume.tex` + `resume.pdf`
- `cover-letter.tex` + `cover-letter.pdf`
- `changelog.md` — every change vs. master resume, with a one-line reason
- `review.md` — output of the review skill
- `interview-prep.md` — output of the prep skill

## Language
- Explain things in simple, clear language. Some users are not native
  English speakers. Avoid jargon, or explain it when unavoidable.
