---
name: analyze
description: Analyze a job posting and map its requirements to the master resume as strong matches, partial matches, or honest gaps. Use when the user provides a job description or URL and wants a fit analysis before tailoring.
---

Analyze the job posting in the user's message. If it contains a URL, use that URL.

Steps:
1. If I gave nothing, ask me to paste the job description.
2. Identify the company name and role. Create the folder `applications/<company-name>/`
   and save the raw posting as `job-description.md`.
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
7. Save everything as `applications/<company-name>/job-analysis.md` and show
   me a short summary: top 5 requirements, my match level for each,
   and one sentence on whether this looks like a good fit and at what seniority.

Do not write the resume yet. That is the tailor step.
