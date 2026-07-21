Create a tailored resume and cover letter for: $ARGUMENTS
(If no company name given, use the most recent folder in `applications/`.)

Requirements:
1. Read `master-resume.tex` and `applications/<company>/job-analysis.md`.
2. Build `resume.tex` in the company folder:
   - Select ONLY the most relevant experience, projects, and skills for the
     top-priority requirements. Cut low-relevance content to fit one page.
   - Reorder so the strongest match appears in the top third of the page.
   - Rewrite bullets to mirror the job's exact keywords, but ONLY where
     truthful. Follow every rule in CLAUDE.md (ATS-safe, no invention).
   - Apply the design rules from CLAUDE.md: titlesec section styling so
     rules never clamp titles; " | " separators; NO em dashes anywhere.
   - Do not duplicate a project or role in two sections. Bootcamps go under
     Education; their capstone projects go under Projects.
3. Build `cover-letter.tex`: maximum 250 words, 3 short paragraphs:
   (a) why this company specifically (use the company research),
   (b) my 2-3 strongest matching achievements with real numbers,
   (c) short, confident closing. Avoid clichés. No em dashes.
4. Compile both to PDF with pdflatex. Fix any compile errors yourself.
   Confirm the resume is ONE page; trim lowest-relevance content if not.
5. Write `changelog.md`: every meaningful change vs. the master resume,
   each with a one-line reason.
6. Show me: the changelog summary and any PARTIAL/GAP items you had to
   handle, so I can verify nothing is exaggerated.
