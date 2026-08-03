Analyze this job posting. The job description (or a URL to it) follows:

$ARGUMENTS

Steps:
1. If I gave a URL, fetch it. If I gave nothing, ask me to paste the job description.
2. Identify the company name and the exact role title. Create the folder
   `applications/<company>-<position>/`, following the naming rule in the
   "File organization" section of CLAUDE.md, and save the raw posting there
   as `job-description.md`. Always include the position in the folder name,
   so a later application to another role at the same company cannot
   overwrite this one.
3. Extract the requirements and rank each by priority 1-10 based on how much
   the posting emphasizes it (mentioned in title/first lines = high; "nice to
   have" = low).
4. List the exact keywords and phrases the posting uses (skills, tools,
   methods, certifications). These matter for ATS keyword matching.
5. Read `master-resume.tex` and map each requirement to my real experience:
   - STRONG match (I clearly have it)
   - PARTIAL match (related experience, needs careful wording)
   - GAP (I don't have it — be honest, do not invent)
6. If you have web access, briefly research the company: what they do,
   recent news, culture signals. 5-8 lines maximum.
7. Save everything as `job-analysis.md` in that same folder, with the exact
   role title as the first heading, and show
   me a short summary: top 5 requirements, my match level for each, and one
   sentence on whether this looks like a good fit and at what seniority.

Do not write the resume yet. That is the /tailor step.
