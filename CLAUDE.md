# Certification exam prep

This repo is a study workspace for certification exams, driven by Claude Code.

- Exam profiles: `exams/<id>/exam.md` (facts, domains, weights, scenarios) and `exams/<id>/coaching.md` (topics, traps). Shared coaching rules: `exams/_shared/coaching.md`.
- Active exam: the `exam:` value in `CLAUDE.local.md` (personal, git-ignored). If it is missing, suggest `/setup-exam`.
- Learner state: `progress/<id>/readiness.md`, one file per exam. Read the active exam's file at the start of every session that involves studying, quizzing or explaining exam topics, and tailor explanations and examples to its weak points. Only `/drill` and `/mock` write to progress files, unless I ask otherwise.
- Practice: `/drill [exam-id] [focus]` runs a 15-question session and updates that exam's progress file.
- Mock exam: `/mock [exam-id] [hard]` runs a full-length, timed mock with no feedback until submit, then updates that exam's progress file.
- New exam: `/setup-exam <id>` with the official exam guide.
- The official exam guide wins over anything written in this repo.
