---
description: Run a 15-question practice drill for the active exam and update its progress file
argument-hint: "[exam-id] [optional focus, e.g. D4 or \"MCP\"]"
allowed-tools: Read, Write, Edit, Glob
---

You are running one drill session for a certification exam.

## 0. Resolve the exam

Arguments: `$ARGUMENTS`

1. Available exams are the folders under `exams/` whose name does not start with `_` and that contain `exam.md`.
2. If the first argument token equals an available exam id, that is the exam, and the rest of the arguments are the focus.
3. Else, if the first token looks like an exam id (lowercase letters/digits with a hyphen, e.g. `ccdv-f`) but no such folder exists, **stop**: say the exam is unknown, list the available exams, and suggest `/setup-exam <id>`. Do not create any file.
4. Otherwise all arguments are the focus, and the exam is the `exam:` value in `CLAUDE.local.md`.
5. If no exam is resolved (no argument and no default), list the available exams and ask which one to use. If there are none, suggest `/setup-exam` and stop.

Below, `<id>` is the resolved exam id.

## Sources

- Exam profile: `exams/<id>/exam.md`: domains (ID, name, weight), score_min, score_max, pass_mark, scenarios.
- Coaching rules: `exams/_shared/coaching.md` plus `exams/<id>/coaching.md`. Follow their question design, anti-guessing gates, feedback, topic coverage and anti-pattern traps. The rules in this file override any delivery instructions there.
- Learner state: `progress/<id>/readiness.md`. If it is missing or shows `Sessions: 0`, this is the first session: run a diagnostic, then create it at the end from `progress/readiness.template.md`, filled with this exam's code, domains and score scale.
- Read and write only this exam's progress file. Never touch `progress/<other-id>/`.
- Optional focus: if present, give that domain or topic at least 6 of the 15 questions.

Weights below are the profile weights as fractions (e.g. 27% → 0.27).

## 1. Plan the session (silently)

Do not show the plan or any answers yet. Pick 15 questions, distributed as follows.

- **Diagnostic (first session):** split 15 by weight, rounding by largest remainder so the total is exactly 15 and every domain gets at least 1.
- **Later sessions:** give each domain at least 1 question. Split the remaining questions in proportion to `weight × (1 − rolling accuracy)`, rounding by largest remainder so the total is exactly 15. Treat a domain with fewer than 5 answers as 50% accuracy. Inside a domain, aim at the "Weak points" and "Next drill focus" items first.
- Ground every question in one of the profile's scenarios. Use 4 options. Make 2–3 questions "select TWO" and include at least 2 reversals.
- Do not reuse any stem listed under "Covered scenarios" in the progress file.
- Write all 15 questions before showing the first one. Check them against the anti-guessing gates, which for 15 questions means:
  - the correct option is the longest in at most 5 of them;
  - correct letters are spread across A/B/C/D, with no letter correct more than 5 times;
  - absolute words (always, never, only, all) are balanced between correct and wrong options.

  Fix any question that fails a gate.

## 2. Run the session (in the terminal, one question at a time)

- Show a header for each question, e.g. `Q3/15 · D4 <Domain name> · Scenario: <scenario>`, then the stem and options A–D. For a "select TWO" question, say so in the stem.
- Wait for the answer. Accepted formats:
  - `B`: single answer
  - `AC`: select TWO
  - `B?`: answer and flag the question
  - `skip`: counts as wrong
  - `stop`: end the session early and still save the results
- After each answer, give instant feedback (practice mode):
  - ✅ or ❌, and the correct option(s);
  - for a miss or a flagged question: what I picked and why it was tempting (name the trap type), the cue I missed, the rule in one line, an analogy and a mnemonic;
  - for a correct, unflagged answer: the rule in one line only.
- Keep a running tally internally, but don't show the score until the end.

## 3. Close the session

1. Show a summary: score x/15 (and %), a per-domain breakdown, the trap types I fell for, and 3 rules to remember.
2. Update `progress/<id>/readiness.md` following `progress/readiness.template.md`. Merge with the existing content; never drop history. Replace placeholder lines (e.g. `_None yet._`) as soon as a section gets real entries.
   - Append this session's ✓/✗ to each domain's **Recent** string. Keep only the last 20 per domain.
   - **Rolling accuracy** = ✓ count / length of the Recent string. Show `—` for a domain with no answers.
   - **Estimated score** = `round(score_min + (score_max − score_min) × Σ(weight × rolling accuracy))`. Treat a domain with no answers as 50% in this formula. Mark it "low data" while any domain has fewer than 5 answers.
   - **Verdict:**
     - **Not ready:** estimated score below `pass_mark`.
     - **Borderline:** `pass_mark` or higher, but below the practice bar.
     - **Drill-ready:** rolling accuracy ≥ 90% overall and ≥ 80% in every domain, with at least 10 answers per domain.
     - Always repeat the booking rule: two full timed mocks at ≥ 90% are still required, and AI-written questions feel easier than the real exam.
   - **Strong points:** concrete task-level rules I answer reliably, each with its evidence (e.g. 6/6).
   - **Weak points:** the most frequent miss patterns, written as `cue → rule`, each with a miss count, its trap type and a streak. A correct answer on that point raises the streak by 1; a miss resets it to 0. Remove the item when the streak reaches 3/3.
   - Write Next drill focus, the session log, covered scenarios and cheat sheet as the template shows.
3. Tell me the new estimated score and verdict in one line.
