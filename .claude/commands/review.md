Review the application for: $ARGUMENTS
(If no company name given, use the most recent folder in `applications/`.)

Act as two different reviewers and be strict. Save results as `review.md`
in the company folder and show me the summary.

REVIEWER 1 — Skeptical recruiter (30-second test):
- Read only the top third of resume.pdf. Is the match with the job obvious
  in 10 seconds? If not, say exactly what to move up.
- Flag anything vague, exaggerated, or buzzword-empty.
- Flag any bullet without a concrete result where the master resume
  actually contains a number we forgot to use.
- Read the cover letter: would a tired recruiter finish it? Flag clichés.

REVIEWER 2 — ATS robot test:
- Extract raw text from resume.pdf (pdftotext or a script).
- Check: does the text read top-to-bottom in correct order? Are name,
  email, and phone present in the extracted text? Are section headings
  standard? Is the date format consistent?
- Compare extracted text against the keyword list in job-analysis.md.
  Report keywords covered and keywords missing. For each missing keyword,
  classify honestly: (a) wording problem — I have the experience, we can
  rephrase truthfully, or (b) real gap — do NOT add it.

Finish with:
- A score out of 10 for recruiter-readability and out of 10 for ATS.
- A short numbered fix list. Ask me if I want you to apply the fixes.
