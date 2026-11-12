# Claude cert prep

A study workspace for certification exams, run from [Claude Code](https://code.claude.com/docs). Claude drills you with scenario questions, explains every miss, and tracks your readiness for each exam in a Markdown file.

## Get started

1. Click **Use this template → Create a new repository** (make it private) and clone it.
2. Open the repo in Claude Code and run `/setup-exam ccar-f` (or another exam id, see below).
3. Run `/drill`. The first session is a 15-question diagnostic; later sessions focus on your weak domains.

Commit `progress/` in your own repo so your history is kept.

## Exams

| Id | Exam | Profile |
| --- | --- | --- |
| `ccar-f` | Claude Certified Architect – Foundations | ready |
| `ccdv-f` | CCDV-F (developer) | not yet: run `/setup-exam ccdv-f` with the official exam guide |

To add any other exam, run `/setup-exam <id>` and attach the official exam guide (PDF path or URL). Profiles are built only from the official guide, never from memory.

## Commands

- `/drill [exam-id] [focus]`: 15 questions, one at a time, with instant feedback. Examples: `/drill`, `/drill D4`, `/drill ccdv-f "MCP"`. Answers: `B`, `AC` (select two), `B?` (flag), `skip`, `stop`.
- `/setup-exam [exam-id] [guide]`: pick the default exam, or add a new exam profile.

## Layout

```
CLAUDE.md                   instructions Claude reads in every session
CLAUDE.local.md             your default exam (git-ignored; see CLAUDE.local.md.example)
.claude/commands/           /drill and /setup-exam
exams/_shared/coaching.md   coaching rules for every exam
exams/_template/            blank profile for a new exam
exams/<id>/exam.md          facts, domains, weights, scenarios
exams/<id>/coaching.md      topics, typical traps, resources
progress/<id>/readiness.md  your progress for that exam (created by the first /drill)
```

## When to book

The progress file gives an estimated score and a verdict. Practice is stricter than the exam: aim for 90% overall and at least 80% in every domain, with at least 10 answers per domain. Then pass two full timed mocks at 90% or higher before you book. AI-written questions feel easier than the real exam, so check any doubtful answer against the official docs.

If you improve the drill engine or a profile, send it back to the template repo so everyone gets it.
