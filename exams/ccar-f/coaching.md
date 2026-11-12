# CCAR-F coaching add-on

Exam-specific rules. Use together with `exams/_shared/coaching.md`; this file adds the topics to cover and the traps that are typical for this exam.

## Topic coverage by domain

Cover every domain's core across sessions:

- **D1 Agentic Architecture & Orchestration:** workflow vs agent; single agent vs coordinator + subagents; the agent loop and `stop_reason` handling; subagents do not inherit parent context (pass it explicitly); task decomposition; escalation and human-in-the-loop gates.
- **D2 Claude Code Configuration & Workflows:** CLAUDE.md hierarchy and precedence (user / project / directory), path-scoped rules, Skills and slash commands, subagents, Plan Mode, hooks vs instructions, permissions, headless `-p` in CI/CD, structured output in CI.
- **D3 Prompt Engineering & Structured Output:** explicit criteria over vague asks, few-shot for consistency, tool_use + JSON schema for structured output, validate-and-retry loops, batch vs synchronous calls, multi-pass review.
- **D4 Tool Design & MCP Integration:** tool descriptions drive selection; fewer, sharper tools beat tool bloat; `tool_choice` modes (auto / any / forced tool); structured tool errors (transient vs validation vs business vs permission) with `isError`; MCP servers vs built-in tools; project vs user MCP config.
- **D5 Context Management & Reliability:** preserving case facts across long sessions, summarization that loses specifics, lost-in-the-middle, scratchpad / state files, escalation handoff summaries, prompt caching (stable prefix first), confidence calibration and when human review is required.

## Reversals worth drilling

The single agent beats multi-agent; the plain prompt is enough; the simple workflow beats the agent.

## Anti-pattern traps (make these the tempting wrong answers)

- "Add it to the system prompt" when a hook, permission, schema or forced `tool_choice` must guarantee the behaviour.
- Routing or escalating on the model's self-reported confidence.
- Bigger context window or bigger model instead of decomposition or better retrieval.
- Multi-agent where one agent with good tools is enough (and the reverse: one agent drowning in 20+ tools).
- Subagents assumed to see the coordinator's history.
- Free-text parsing where tool_use + JSON schema belongs.
- Retrying a validation or permission error as if it were transient.
- Summarizing away the exact numbers, IDs and dates the task needs.
- Personal/local config where shared project config (or managed settings) is needed for a team.

## Official resources

- CCAR-F Exam Guide (domains, task statements, scenarios, sample questions)
- Anthropic Academy prep path: anthropic-partners.skilljar.com
- Claude Platform docs (platform.claude.com/docs): tool use, structured outputs, prompt caching, prompting
- Claude Code docs (code.claude.com/docs): CLAUDE.md, settings, permissions, hooks, Skills, subagents, headless mode
- Claude Agent SDK docs and the MCP spec
- "Building effective agents" (anthropic.com/engineering)

Model names, prices and limits change. Learn the decision method, and check current values in the docs shortly before the exam.
