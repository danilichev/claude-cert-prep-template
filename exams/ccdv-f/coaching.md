# CCDV-F coaching add-on

Exam-specific rules. Use together with `exams/_shared/coaching.md`; this file adds the topics to cover and the traps that are typical for this exam.

## Topic coverage by domain

From the guide's skill descriptions (skill weights are each skill's share of the whole exam):

- **D1 Agents and Workflows (14.7%):** Agent Architecture 4.5% (workflow vs agent decision criteria, manager/supervisor hierarchies, role of subagents); Agent Construction with Claude 5.3% (Claude Agent SDK, custom agent loops and harnesses, managed agent deployment self-hosted vs Anthropic-hosted, hooks for deterministic actions); Agent Patterns and Frameworks 4.9% (tool-use loops, sub-agents, memory, context-window management, frameworks such as Strands, LangGraph, PydanticAI).
- **D2 Applications and Integration (33.1%):** Understanding Requirements 3.4% (functional and infrastructure requirements from business requirements and solution architecture); Systems Life Cycle 2.8% (develop, implement, operate, maintain); Claude API Mechanics 6.8% (messages, tools, streaming, vision, thinking, caching, third-party vendors, Messages API data access patterns, batch API, realtime vs batch); Software Engineering Foundations 7.4% (REST, JSON, async, version control, SDLC integration, code review, small- and large-scale refactoring); Claude Application Design 8.6% (how Claude interprets instructions across Claude Code, Desktop, claude.ai, API, SDKs; content boundaries, schema design, session hygiene, plugin management); Configuration Management 4.1% (CLAUDE.md, settings.json, model version pinning, prompt versioning, plugin dependencies).
- **D3 Claude Code (3.1%):** Rules, Skills, Commands, Agents, Agent Memory; session management, built-in and custom slash commands, headless mode, streaming mode, auto-mode; CLAUDE.md hierarchy, repository initialization, settings.json.
- **D4 Eval, Testing, and Debugging (2.6%):** error type identification, recovery strategy selection, trace analysis to find failure modes, isolating integration-layer vs model-output problems.
- **D5 Model Selection and Optimization (16.8%):** LLM Fundamentals 5.2% (tokens, context windows, sampling, non-determinism, next-token generation; fast mode, extended thinking, adaptive thinking, effort levels; zero-, single-, multi-shot); Technical Fundamentals 6.1% (SDKs that wrap REST APIs, websockets); Model Selection and Tradeoffs 2.7% (Opus vs Sonnet vs Haiku, adaptive thinking support, quality/latency/cost, breaking behavior changes across releases); Cost and Token Management 2.8% (token usage tracking, cost modeling, prompt caching, cache check-pointing).
- **D6 Prompt and Context Engineering (11.0%):** Context Engineering 3.8% (context window management, drift and bloat prevention via tool-output pruning and compaction, context isolation via subagents or multi-step workflows); Prompt Engineering 4.6% (instruction clarity, few-shot, system vs user placement, output constraints, instruction placement across components, iterative refinement, input sanitization); Output Handling 2.6% (structured output patterns, response validation, defensive parsing, skepticism toward confident output).
- **D7 Security and Safety (8.1%):** AI Application Security 3.2% (prompt injection, jailbreak defense, untrusted input, data leakage, PII, authN/authZ, confidentiality, integrity); Guardrails and Safe Deployment 2.3% (content policy, guardrail layering, privacy, IAM, least privilege); Claude Hooks 1.0% (hooks that block destructive actions); Identity, Secrets, and Key Management 1.6% (API keys and credentials across dev and prod, identity validation, access approval, access monitoring).
- **D8 Tools and MCPs (10.6%):** Tool Implementation 4.4% (function calling, external system configuration, tool descriptions, error handling, agentic harness dispatch, client-side vs server-side tools, approval patterns, tool set construction); MCP Server Development 2.1% (authoring, deployment, integration, MCP resources / tools / prompts, stdio vs sockets, client vs server); Agentic Customization 4.1% (built-in tools vs custom tools vs Skills vs MCPs).

Weighting note: D2 alone is a third of the exam, and D3 + D4 together are under 6%. Do not over-drill Claude Code trivia.

## Reversals worth drilling

- The plain workflow beats the agent when the steps are known in advance.
- A smaller, cheaper model is the right answer when the task is simple and latency or cost matters; a bigger model is not a fix for injection or bad prompts.
- A more instruction-following model can be *more* susceptible to prompt injection, not less (guide, sample 2).
- A built-in tool or a Skill beats building an MCP server when there is no need to share the capability across apps.

## Anti-pattern traps (make these the tempting wrong answers)

- Synchronous parallel calls, lower `max_tokens` or the smallest model when the requirement is latency-tolerant bulk work (the answer is the Message Batches API).
- "Add a line to the system prompt" or "ask users not to" where untrusted-input isolation, least privilege, a hook or a permission must enforce it.
- Changing temperature or sampling to fix a security or correctness problem.
- Bigger model or bigger context window instead of context pruning, compaction, subagent isolation or better retrieval.
- Pasting live data into the context on every request instead of a tool or MCP server.
- Assuming built-in tools can reach arbitrary internal APIs.
- Hard-coding reusable logic into each app's prompt instead of a shared tool/MCP server.
- Trusting confident model output without schema validation or defensive parsing.
- Retrying a validation, auth or permission error as if it were transient; blaming the model for an integration-layer bug (or the reverse).
- Floating model aliases in production where a pinned model version is needed; unversioned prompts.
- API keys in code, prompts, CLAUDE.md or client-side apps instead of a secrets manager / server-side calls.
- Prompt caching with a changing prefix (dynamic content placed before the stable part).
- Multi-agent hierarchy where one agent with good tools is enough.

## Official resources

- CCDV-F Exam Guide v1.0 (July 2026): blueprint, 3 sample questions (illustrative, not from the live item bank), policies
- Anthropic Partner Academy prep path: anthropic-partners.skilljar.com/path/claude-developer
- Official Anthropic docs the guide names: Claude API, models, prompt engineering, Claude Code, Skills, MCP
- Hands-on: build one Claude app that uses the API, one or more tools, basic prompt/context engineering, and simple security and eval practices (guide, section 7)

Model names, prices and limits change. Learn the decision method, and check current values in the docs shortly before the exam.
