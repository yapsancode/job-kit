# Contributing

Thanks for wanting to improve Resume Tailor! This project stays useful only
if it keeps two promises, so please preserve both in any contribution:

1. **ATS-safe** — no multi-column layouts, tables, text boxes, images, or
   icons in the resume template. These break automated resume parsers.
2. **Never invent** — skills and rules must never encourage fabricating
   experience, skills, or numbers. Honesty about gaps is a feature.

## Good things to contribute

- **Better LaTeX templates** — cleaner typography that stays ATS-safe.
- **New skills** — e.g. `networking` (draft LinkedIn outreach),
  `skillgap` (given target jobs, suggest the next skill to learn).
- **Localisation** — clearer language, or skill variants for non-English
  job markets.
- **Docs** — setup help for specific OSes or LaTeX distributions.

## How to contribute

1. Fork the repo and create a branch.
2. Make your change. Skills are mirrored for tool compatibility, so every
   change must be identical in `.agents/skills/<name>/SKILL.md` and
   `.claude/skills/<name>/SKILL.md`. If you touch the resume template,
   compile it and run a `pdftotext` check to confirm it still reads
   top-to-bottom.
3. Open a pull request describing what you changed and why.

## Reporting issues

Open an issue with your OS, your LaTeX distribution, the skill you invoked,
and what happened. Screenshots of the rendered PDF help a lot.
