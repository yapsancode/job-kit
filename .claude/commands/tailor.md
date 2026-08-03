Create a tailored resume and cover letter for: $ARGUMENTS
Application folders are named `<company>-<position>`, so match that against
the folder names in `applications/`. If the company has more than one folder
and the argument does not say which role, ask which one.
(If nothing is given, use the most recent folder in `applications/`.)

Requirements:
1. Read `master-resume.tex` and `job-analysis.md` in that application folder.
2. Build `resume.tex` in the application folder, keeping that exact filename:
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
4. Compile both to PDF with pdflatex, giving each PDF a recruiter-friendly
   filename via `-jobname` (see "Naming the compiled PDFs" in CLAUDE.md).
   Take the candidate's full name from `master-resume.tex` and the position
   from `job-analysis.md`:

       pdflatex -jobname="Jordan-Ruiz-Resume-Agentic-AI-Engineer" resume.tex
       pdflatex -jobname="Jordan-Ruiz-Cover-Letter-Agentic-AI-Engineer" cover-letter.tex

   Leave the `.tex` sources named `resume.tex` and `cover-letter.tex`.
   Fix any compile errors yourself.
   Confirm the resume is ONE page; trim lowest-relevance content if not.
   If an earlier run left a `resume.pdf` or `cover-letter.pdf` in the folder,
   delete it so only the correctly named PDFs remain.
5. Write `changelog.md`: every meaningful change vs. the master resume,
   each with a one-line reason.
6. Show me: the two PDF filenames you produced, the changelog summary, and
   any PARTIAL/GAP items you had to handle, so I can verify nothing is
   exaggerated.
