---
description: Run a full-length, timed mock exam for the active exam and update its progress file
argument-hint: "[exam-id] [hard]"
allowed-tools: Read, Write, Edit, Glob, Bash(date:*)
---

You are running one full-length mock exam under real exam conditions: the exam's question count and time limit, and no feedback until I submit.

## 0. Resolve the exam and mode

Arguments: `$ARGUMENTS`

1. Available exams are the folders under `exams/` whose name does not start with `_` and that contain `exam.md`.
2. If an argument token equals an available exam id, that is the exam. If a token is `hard`, this is a hard mock. Any other token: say it is not understood, show the usage `/mock [exam-id] [hard]`, and stop.
3. If a token looks like an exam id (lowercase letters/digits with a hyphen, e.g. `ccdv-f`) but no such folder exists, **stop**: say the exam is unknown, list the available exams, and suggest `/setup-exam <id>`. Do not create any file.
4. Otherwise the exam is the `exam:` value in `CLAUDE.local.md`. If there is none, list the available exams and ask which one to use. If there are none, suggest `/setup-exam` and stop.

Below, `<id>` is the resolved exam id, `N` is `exam_questions` and `T` is `exam_minutes` from `exams/<id>/exam.md`.

## Sources

- Exam profile: `exams/<id>/exam.md`: N, T, domains (ID, name, weight), score_min, score_max, pass_mark, scenarios.
- Coaching rules: `exams/_shared/coaching.md` plus `exams/<id>/coaching.md`. Follow their question design, anti-guessing gates, feedback, topic coverage and anti-pattern traps. The rules in this file override any delivery instructions there.
- Learner state: `progress/<id>/readiness.md`. If it is missing, create it at the end from `progress/readiness.template.md`, filled with this exam's code, domains and score scale.
- Read and write only this exam's progress file. Never touch `progress/<other-id>/`.

## 1. Plan the mock (silently)

Do not show the plan or any answers until I submit.

- Split N questions across domains by weight, rounding by largest remainder so the total is exactly N and every domain gets at least 1. Do **not** bias towards my weak domains: a mock measures, it does not train.
- Within each domain, cover as many different topics from the coaching add-on as possible.
- Ground every question in one of the profile's scenarios, spread across all of them. Use 4 options. Make about 15% of the questions "select TWO" and about 10% reversals.
- **Standard mock:** difficulty matching the official sample questions.
- **Hard mock:** every wrong option is a strong trap (partial fix, right idea at the wrong layer, or right mechanism that acts too late); about 25% "select TWO" and 20% reversals; longer stems with one decisive cue among irrelevant detail.
- Do not reuse any stem listed under "Covered scenarios" in the progress file.
- Write all N questions before showing the first one, with each one's domain, correct answer, and trap type for every wrong option. Check the whole set against the anti-guessing gates:
  - the correct option is the longest in at most 35% of questions;
  - mean length ratio of correct to wrong options is 0.95–1.05;
  - correct letters are spread across A/B/C/D, with no letter correct in more than 30% of questions;
  - absolute words (always, never, only, all) are balanced between correct and wrong options.

  Fix any question that fails a gate.
- Shuffle the domains so consecutive questions rarely share a domain, as on the real exam.

## 2. Run the mock (exam mode)

1. Tell me the rules in three lines: N questions, T minutes, no feedback until submit, and the accepted inputs below. Ask me to type `start`.
2. On `start`, run `date +%s` and remember it as the start time. The deadline is start + T × 60.
3. Before showing each question, run `date +%s` and show a header like `Q12/53 · ⏱ 87 min left · Answered 11 · Flagged 2`. Do **not** show the domain or the scenario name in the header; the real exam doesn't.
4. Show the stem and options A–D. For a "select TWO" question, say so in the stem.
5. Accepted inputs:
   - `B`: answer
   - `AC`: answer a "select TWO" question
   - `B?`: answer and flag for review
   - `?` or `skip`: leave unanswered and flag for review
   - `goto 17`: jump to a question (to revisit an answer)
   - `review`: list every question number with its status (answered / flagged / unanswered), no answers or correctness shown
   - `submit`: end the mock and score it
   - `stop`: abandon the mock (see step 4 of section 3)
6. Give no feedback, hints or reactions to any answer. If my input is not valid (e.g. one letter on a "select TWO" question), say what is expected and show the same question again.
7. After question N, show the `review` list and ask me to revisit flagged or unanswered questions, or to `submit`.
8. If the deadline has passed when an input arrives, do not record it: tell me time is up and submit automatically. Unanswered questions count as wrong.

## 3. Score and review

1. Run `date +%s` to get the time used.
2. Show the result:
   - raw score x/N (and %), time used out of T;
   - **estimated scaled score** = `round(score_min + (score_max − score_min) × Σ(weight × domain accuracy in this mock))`, and pass/fail against `pass_mark`;
   - a per-domain table: questions, correct, accuracy, and ✅ / ⚠️ against the 80% domain bar;
   - **booking bar:** met if overall ≥ 90% and every domain ≥ 80%. Say whether this mock counts towards the booking rule (it counts only when met), and how many qualifying mocks I now have and whether the latest is hard.
   - the trap types I fell for, with counts.
3. Then review every missed and every flagged question, in question order: the question number, the stem in one line, what I picked and why it was tempting (name the trap type), the correct option(s), the cue I missed, the rule in one line, an analogy and a mnemonic. For correct, unflagged questions show nothing. Finish with the 5 rules to remember.
4. If I typed `stop`: score the answered questions only, mark the mock **abandoned**, never count it towards the booking rule, and still save it.

## 4. Update the progress file

Update `progress/<id>/readiness.md` following `progress/readiness.template.md`. Merge with the existing content; never drop history. Replace placeholder lines (e.g. `_None yet._`) as soon as a section gets real entries.

- Add a row to **Mock log**: number, date, mode (standard / hard), raw score, estimated scaled score, time used, per-domain accuracy, and whether it counts (yes / no / abandoned).
- Update the **Booking rule** line in the snapshot: qualifying mocks so far, and whether the latest qualifying one is hard.
- Feed the answers into the domain stats exactly as `/drill` does (section 3 step 2 of `.claude/commands/drill.md`): Recent strings (last 20 per domain), rolling accuracy, estimated score, verdict, strong points, weak points with streaks, traps, next drill focus, covered scenarios and cheat sheet. Unanswered questions count as ✗.
- Do not add the mock to the **Session log**; that table is for drills. Count the mock in the header's `Questions answered`, not in `Sessions`.

Finish with one line: the mock result, the new estimated score and verdict, and the booking-rule status.

The mock lives in this conversation only. If the session is closed mid-mock, nothing is saved; start again with `/mock`.
