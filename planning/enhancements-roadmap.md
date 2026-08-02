# Enhancement ideas: what to build next

Status: **discussion only — nothing agreed for implementation yet.**
Captured from a planning conversation on Aug 3, 2026. Open questions at
the bottom need answers before any of this is built.

## The big picture

Every current skill helps *before* the application is sent
(`analyze -> tailor -> review -> prep`). Almost nothing helps *after*.
The strongest theme across all ideas below is **closing the loop**:

- job analyses feed an upskill roadmap,
- application outcomes feed search strategy,
- real interview questions feed future prep.

A tailoring tool is a commodity; a system that learns from your own
search history is sticky. That loop is also the strongest argument for
the paid web app idea later.

## Ideas from the maintainer (with assessment)

### 1. Upskill roadmap + course finder — TOP PICK (merge of two ideas)
A `/roadmap` skill that:
- reads all `applications/*/job-analysis.md` files and counts which
  gaps keep appearing ("Docker was a gap in 5 of 7 applications"),
- ranks gaps by how many desired jobs each one unlocks,
- suggests courses/certs for the top gaps via web search (so links
  stay current instead of hardcoded),
- closes the loop with the existing `/log` skill when something is
  finished.

Why first: it creates new value from data the project already produces,
and turns job-kit from "help me apply" into "help me become more
hireable" — a bigger promise.

### 2. Enhance the review skill
"Enhance" needs a definition. Candidate improvements discussed:
- **Truthfulness diff (preferred first pick)**: cross-check every claim
  in the tailored resume against `master-resume.tex` to catch
  accidental exaggeration. Directly enforces the ground-truth rule
  automatically instead of relying on discipline.
- Numeric score with a fixed rubric, so reviews are comparable
  between applications.
- Keyword coverage check: which exact job-description terms appear in
  the resume and which don't.
- Re-review mode: a second pass after fixes.

### 3. Direct-message outreach — draft-only, hard line
Drafting personalized outreach (recruiters, hiring managers) from the
resume + job analysis is genuinely valuable — referrals beat cold
applications. But the skill must only **draft**; the user sends it
themselves. Auto-sending is spam territory, risks LinkedIn account
restriction, and one wrongly-personalized message to a real recruiter
is very costly.

### 4. Find related jobs — updated after reviewing kerja-it.com
Original position: big job boards (LinkedIn, Indeed) block scraping and
it breaks constantly, so the realistic version was only a "query
advisor" (suggest search terms and alternative titles, user searches by
hand).

**Update (Aug 3, 2026):** reviewed a friend's project,
https://github.com/afrieirham/kerja-it.com — a Malaysian tech job
board. Key insight: it does NOT scrape. It uses the official **Google
Custom Search API** to find job pages published in the last ~24h
matching a list of job titles + Malaysian states, stores the metadata
Google returns (title, description, URL), and links out to the original
posting. LinkedIn jobs appear because Google indexed them — the code
never touches LinkedIn's servers. Legally safer and far more stable
than scraping.

That opens two real options for job-kit, better than the query advisor:

- **Option A — same technique, personalized:** a skill that builds
  Google Custom Search queries from the user's own master resume
  (their titles, skills, location) and pulls fresh matching postings.
  Needs a Google API key (free tier: 100 queries/day). Limitation:
  only metadata snippets, not full job descriptions, so deep matching
  still needs the user to fetch the posting.
- **Option B — partner with kerja-it:** it already has the pipeline,
  database, and a daily Telegram digest, but shows everyone the same
  list. job-kit's strength is matching. Pulling new kerja-it jobs and
  running `analyze` against the master resume ("3 new jobs this week
  where you're a strong match") gives a personal job alert system
  neither project has alone. Worth a conversation with the friend;
  could also feed the web app idea.

**Constraint (from the maintainer): must not be tech-only.**
kerja-it hardcodes tech job titles and works for one industry. job-kit
serves whoever's `master-resume.tex` is in the repo — nursing, finance,
logistics, anything. So:
- Search titles/keywords must be **derived from the master resume**
  (plus past analyses), never from a hardcoded tech list.
- Location list should also be configurable, not fixed to Malaysia.
- This makes Option A the more general design; Option B works as an
  extra source when the user's field is tech, not as the foundation.

## Additional ideas raised in discussion

### 5. Application tracker — top pick among the new ideas
One folder per company already exists, but nothing records what
happened next. Add a `status.md` per application (applied on date X,
heard back, interview scheduled, rejected, offer) plus a `/pipeline`
skill: "7 applications, 2 waiting more than 14 days, 1 interview this
week." Could pair with a follow-up reminder ("applied 10 days ago,
no reply — time to follow up"). Job searching is mostly a
memory-and-discipline problem; this solves that.

### 6. Interview debrief — unique data that compounds
After each real interview, run `/debrief` to record which questions
were actually asked, what went badly, what was surprising. Payoffs:
`prep` gets smarter (real questions, not guessed ones), and over time
a personal question bank per role type builds up.

### 7. Outcome analytics (needs the tracker first)
Once outcomes are recorded, answer "what's working?": which
applications got replies vs. silence, whether strong-match applications
actually convert better, whether a role type never responds. Patterns
show up even with 10-15 applications.

### 8. Achievement journal nudge
`/log` exists but is easy to forget — people remember achievements only
when writing a resume, the worst time. A light recurring prompt
("anything worth logging this month?") keeps `master-resume.tex`
fresh, which is what makes every other skill work.

### 9. LinkedIn profile sync
Recruiters cross-check resume vs. LinkedIn; mismatch looks sloppy.
Generate LinkedIn-ready text (headline, about, experience blurbs)
straight from `master-resume.tex`. Cheap: basically `tailor` with a
different output format.

### 10. Salary negotiation prep
Nothing in the kit touches the offer conversation — the highest
money-per-minute moment of the search. A `/negotiate` skill: research
typical ranges for the role, produce truthful talking points grounded
in the master resume. Small skill, potentially worth thousands.

## Explicitly skipped
- Company research as its own skill — fold into `prep` instead.
- Resume design/themes — ATS-safe plain formatting is a feature,
  not a gap.
- Anything that auto-submits applications or auto-sends messages —
  same spam risk as auto-DM, and quality drops.
- Automatic job-board scraping — against ToS, brittle.

## Suggested build order
1. **`/roadmap`** (gaps -> priorities -> courses) — new value from
   existing data.
2. **Application tracker + `/pipeline`** — immediately useful, easy.
3. **Review enhancement** — start with the truthfulness diff.
4. **`/debrief`** — small, and its value compounds.
5. Outreach drafter, LinkedIn sync, related-jobs search (Google Custom
   Search API, industry-agnostic — see idea 4), negotiation prep,
   analytics, journal nudge — as needed after the above.

## Open questions (answer before building)
- Review skill: which weakness has actually been felt in use — vague
  feedback, missed keywords, accidental exaggeration, or something
  else?
- Roadmap: free courses only, or paid ones too (Coursera, Udemy,
  cert exams)?
- How many applications exist in `applications/` right now? If only
  one or two, "recurring gaps" has little data — start simpler.
