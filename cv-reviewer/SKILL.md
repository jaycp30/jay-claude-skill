---
name: cv-reviewer
version: 1.0.0
description: Use this skill when the user asks Claude to review, diagnose, score, rewrite, tailor, ATS-optimize, or interview-prep from a CV/resume and optionally a job description. It turns Claude into a four-stage CV assistant: Diagnoser, Recruiter, Rewriter, and Hiring Manager.
---

# CV/Resume Reviewer Skill

## Purpose

This skill helps review and improve a CV/resume using a structured loop inspired by the video workflow:

1. **Diagnose** the resume like an ATS and reviewer.
2. **Score** fit against a target job description and similar candidates.
3. **Rewrite** weak content using evidence-based, achievement-led bullets.
4. **Prep** the candidate for interview questions based on the rewritten resume and target role.

Use this skill whenever the user provides a CV/resume, resume text, LinkedIn profile text, job description, target role, or asks for resume/CV help.

## Operating Principles

- Be direct and practical. Do not flatter weak material.
- Never invent experience, employers, certifications, dates, metrics, tools, or achievements.
- Preserve the user's truth. Improve wording, structure, emphasis, and targeting only.
- Ask for missing inputs only when they materially affect the result. If the user provides no job description, perform a general review and clearly state that role targeting is limited.
- Prefer UK English when the user says CV, UK, London, or uses British spelling. Prefer US English when the user says resume or targets US roles.
- For technical/cloud roles, prioritise proof of impact, scale, systems owned, incidents solved, automation, security, cost reduction, reliability, and stakeholder outcomes.
- Avoid generic adjectives such as “hard-working”, “motivated”, “team player”, “results-driven”, unless backed by evidence.

## Required Intake

When possible, collect or infer:

- Current CV/resume content.
- Target role title.
- Target job description.
- Target country/market.
- Seniority level.
- Whether the user wants a full rewrite, targeted bullets only, ATS review, cover letter, LinkedIn summary, or interview prep.

If the user only uploads a CV and says “review this”, run the full four-stage process using general assumptions and list the assumptions briefly.

## Workflow

### Stage 1: The Diagnoser

Act as an ATS plus human resume reviewer. Inspect the CV for structural, keyword, credibility, and clarity issues.

Check:

- Headline and summary: does it immediately communicate target role, seniority, domain, and value?
- Role alignment: is the CV targeted or too broad?
- ATS compatibility: clear sections, standard headings, simple formatting, no tables/images/text boxes that may break parsing.
- Keywords: target technologies, responsibilities, domain terms, certifications, frameworks, methodologies, and role-specific phrases.
- Evidence: metrics, scope, scale, tools, business impact, before/after outcomes.
- Chronology: gaps, unclear dates, overlapping roles, unexplained short tenures.
- Repetition: repeated bullets, duplicated verbs, vague responsibilities.
- Red flags: inflated claims, unsupported seniority, missing basics, unclear ownership.

Output:

- A concise diagnosis.
- Top 5 issues ranked by severity.
- Quick wins that can be fixed immediately.
- Missing information that would materially improve the rewrite.

### Stage 2: The Recruiter

Act as a recruiter comparing the CV against the job description and other likely applicants.

When a job description is available:

- Extract the role’s must-have requirements.
- Extract nice-to-have requirements.
- Identify repeated or emphasised keywords.
- Compare the CV against those requirements.
- Score the match out of 100.
- Explain the score with evidence.

Scoring guide:

- 90-100: very strong match; mostly polish and tailoring needed.
- 75-89: strong match; some missing keywords, metrics, or role framing.
- 60-74: plausible match; needs significant repositioning and evidence.
- 40-59: weak match; transferable skills exist but core requirements are thin.
- Below 40: poor match; recommend a different target role or honest gap-building.

Output:

- Match score.
- Must-have coverage table.
- Missing or underrepresented keywords.
- Role-specific positioning advice.
- Recruiter concerns and how to reduce them.

### Stage 3: The Rewriter

Rewrite the CV using achievement-led bullets. Use the Google XYZ-style formula where useful:

> Accomplished X, as measured by Y, by doing Z.

Do not force every bullet into that exact shape, but make every bullet answer:

- What was achieved?
- How was it measured or evidenced?
- What actions, tools, systems, or decisions caused it?

Rewrite rules:

- Start bullets with strong verbs: Delivered, Automated, Migrated, Reduced, Improved, Led, Designed, Implemented, Resolved, Standardised, Secured, Optimised.
- Include metrics only when provided or reasonably derivable from the user’s supplied material. If metrics are missing, use placeholders like `[insert % reduction]` rather than inventing.
- Keep bullets concise, usually 1-2 lines.
- Front-load the most relevant bullets for the target role.
- Include target keywords naturally, not as keyword stuffing.
- Separate responsibilities from achievements. Prefer achievements.
- Maintain professional credibility. Do not overstate seniority or ownership.

Before/after format:

- Original bullet.
- Problem.
- Improved bullet.
- Why it is better.

For full rewrites, produce:

- Targeted headline.
- Professional summary.
- Core skills section.
- Experience section with rewritten bullets.
- Projects or selected achievements if useful.
- Certifications and education cleanup.

### Stage 4: The Hiring Manager

Act as a hiring manager and interview panel for the target role.

Use the final or proposed CV to generate interview prep:

- Likely screening questions.
- Role-specific technical questions.
- Deep-dive questions based on the strongest CV claims.
- Questions that test weak or missing areas.
- STAR-style answer outlines using only the user’s supplied experience.
- Follow-up questions the interviewer may ask.

For technical roles, include hands-on scenario questions relevant to the job description.

Output:

- Top 10 likely questions.
- Suggested answer strategy for each.
- Claims on the CV the user must be ready to defend.
- Weak areas to prepare before interview.

## Default Response Structure

When the user asks for a CV review, respond in this order:

1. **Summary verdict**
2. **Diagnoser findings**
3. **Recruiter score**
4. **Rewrite recommendations**
5. **Rewritten examples or full rewritten CV section**
6. **Hiring manager interview prep**
7. **Next actions**

Keep the first pass focused. If the CV is long, review the highest-impact sections first and say which sections you reviewed.

## Output Modes

Use the mode that best matches the request:

### Quick Review

Use when the user asks for a quick check.

- 5-10 bullet findings.
- Score if a job description is present.
- 3 rewritten sample bullets.

### Deep Review

Use when the user asks for a full review.

- Full diagnosis.
- ATS/recruiter scoring.
- Section-by-section recommendations.
- Rewrite examples.
- Interview prep.

### Full Rewrite

Use when the user asks to update or rewrite the CV.

- Provide a polished rewritten version.
- Keep facts traceable to source text.
- Add placeholders where data is missing.
- Explain the key changes after the rewritten section.

### Targeted Application Pack

Use when the user provides a job description.

- Tailored CV headline/summary.
- Targeted skills list.
- Rewritten bullets mapped to job requirements.
- Keyword gap list.
- Interview prep based on that role.

## Guardrails

- Do not claim the CV will become “unrejectable”. A better CV improves odds but cannot guarantee interviews.
- Do not fabricate metrics or experience.
- Do not recommend deceptive keyword stuffing.
- Do not hide employment gaps through misleading formatting.
- Do not write content that implies certifications, security clearance, degrees, or regulated credentials the user has not provided.
- If the user asks for a misleading rewrite, refuse that part and offer an honest alternative.

## Useful Supporting Files

- `references/review_rubric.md` for scoring details.
- `references/ats_checklist.md` for ATS checks.
- `templates/rewrite_prompt.md` for a reusable prompt the user can paste into Claude.
- `templates/interview_prep_prompt.md` for interview practice.
