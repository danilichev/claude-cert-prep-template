---
description: Pick an exam to study, or add a new exam profile from its official guide
argument-hint: "[exam-id] [path or URL to the official exam guide]"
allowed-tools: Read, Write, Edit, Glob, WebFetch
---

Set up an exam for drilling. Arguments: `$ARGUMENTS`

## 1. Pick the exam

- Available exams are the folders under `exams/` whose name does not start with `_` and that contain `exam.md`.
- If no exam id was given, list the available exams and ask which one to use, or whether to add a new one.
- An exam id is lowercase letters/digits with hyphens, usually the official exam code (e.g. `ccar-f`, `ccdv-f`). Reject anything else.

## 2. If the exam profile already exists

Go to step 4.

## 3. If it is a new exam: build the profile from the official guide

1. Get the official exam guide: the path or URL from the arguments, or a file attached in this conversation. If there is none, ask for it and stop. **Never build a profile from memory or third-party guesses.**
2. Read the guide. Create `exams/<id>/exam.md` from `exams/_template/exam.md`:
   - fill every field from the guide;
   - domain weights must add up to 100%;
   - list the guide's production scenarios;
   - if the guide does not state a value (e.g. the score scale), ask me for it. Do not leave `<placeholders>` in the file.
3. Create `exams/<id>/coaching.md` from `exams/_template/coaching.md`:
   - topic coverage per domain, taken from the guide's task statements;
   - reversals and anti-pattern traps typical for this exam (plausible wrong approaches for its domains);
   - official resources the guide mentions.
4. Show me a short summary of both files (domains with weights, pass mark, number of scenarios) before continuing.

## 4. Make it active

- If `CLAUDE.local.md` does not exist, create it from `CLAUDE.local.md.example` with `exam: <id>`.
- If it exists with a different `exam:` value, ask whether to switch the default. Only change that line.
- Do not create `progress/<id>/`. The first `/drill` creates `progress/<id>/readiness.md` after a diagnostic session.

## 5. Finish

Tell me in one or two lines: the active exam, and to run `/drill` (or `/drill <id>`) to start the diagnostic.
