# CCAR-F coaching add-on

Exam-specific rules. Use together with `exams/_shared/coaching.md`; this file adds the topics to cover and the traps that are typical for this exam.

## Topic coverage by domain

From the guide's task statements (section 6). Cover every task statement across sessions:

- **D1 Agentic Architecture & Orchestration (27%):**
  - 1.1 Agentic loop: continue on `stop_reason: "tool_use"`, stop on `"end_turn"`; append tool results to history; model-driven tool choice vs fixed decision trees.
  - 1.2 Coordinator-subagent (hub-and-spoke): all communication routed through the coordinator; dynamic subagent selection; scope partitioning; iterative refinement loops; risk of too-narrow decomposition.
  - 1.3 Subagent spawning: Task tool (`allowedTools` must include `"Task"`); `AgentDefinition` (description, system prompt, tool restrictions); context passed explicitly in the prompt; parallel spawning via several Task calls in one response; goal-and-criteria prompts, not step-by-step procedures.
  - 1.4 Enforcement and handoff: programmatic prerequisites (block `process_refund` until `get_customer` returns a verified ID); multi-concern decomposition; structured handoff summaries for humans without the transcript.
  - 1.5 Agent SDK hooks: PostToolUse to normalize data (timestamps, status codes); tool-call interception to block policy violations (refund > $500) and redirect to escalation.
  - 1.6 Task decomposition: prompt chaining for predictable reviews vs dynamic decomposition for open-ended investigation; per-file passes plus a cross-file pass.
  - 1.7 Sessions: `--resume <session-name>`, `fork_session`, telling a resumed session which files changed, fresh session + structured summary when tool results are stale.
- **D2 Tool Design & MCP Integration (18%):**
  - 2.1 Tool descriptions drive selection: inputs, examples, edge cases, when-to-use vs similar tools; rename/split overlapping tools; keyword-sensitive system-prompt wording.
  - 2.2 Structured MCP errors: `isError`; `errorCategory` (transient / validation / business / permission), `isRetryable`, human-readable text; local recovery in subagents; access failure vs valid empty result.
  - 2.3 Tool distribution: 4–5 scoped tools per agent beat 18; scoped cross-role tools (`verify_fact`); constrained tools (`load_document` over `fetch_url`); `tool_choice` `"auto"` / `"any"` / forced.
  - 2.4 MCP config: project `.mcp.json` (shared, `${ENV_VAR}` expansion) vs user `~/.claude.json` (personal); all servers' tools available at once; MCP resources for content catalogs; community servers over custom ones for standard integrations.
  - 2.5 Built-in tools: Grep (content), Glob (paths), Read/Write (whole file), Edit (unique anchor; fall back to Read + Write); explore incrementally from Grep entry points.
- **D3 Claude Code Configuration & Workflows (20%):**
  - 3.1 CLAUDE.md hierarchy (user `~/.claude/CLAUDE.md` / project / subdirectory); user level is not shared; `@import`; `.claude/rules/`; `/memory` to see what is loaded.
  - 3.2 Commands and skills: `.claude/commands/` (shared) vs `~/.claude/commands/` (personal); `.claude/skills/<name>/SKILL.md` frontmatter `context: fork`, `allowed-tools`, `argument-hint`; skills on demand vs CLAUDE.md always loaded.
  - 3.3 Path-scoped rules: `.claude/rules/*.md` with YAML `paths:` globs (e.g. `**/*.test.tsx`), loaded only when editing matching files; beats subdirectory CLAUDE.md for conventions spread across directories.
  - 3.4 Plan mode (architecture, many files, several valid approaches) vs direct execution (clear, small scope); Explore subagent for verbose discovery; plan then execute.
  - 3.5 Iterative refinement: 2–3 input/output examples, tests first then share failures, interview pattern, one message for interacting issues vs sequential fixes for independent ones.
  - 3.6 CI/CD: `-p` / `--print`; `--output-format json` + `--json-schema`; CLAUDE.md carries CI context; independent reviewer instance; pass prior findings to report only new issues; pass existing tests to avoid duplicates.
- **D4 Prompt Engineering & Structured Output (20%):**
  - 4.1 Explicit categorical criteria beat "be conservative" / "high-confidence only"; disable high false-positive categories to restore trust; severity criteria with code examples.
  - 4.2 Few-shot: 2–4 targeted examples for ambiguous cases with reasoning; format consistency; acceptable-vs-issue contrasts; varied document structures; fixes empty/null extraction.
  - 4.3 Structured output via `tool_use` + JSON schema (no syntax errors, but semantic errors remain); `tool_choice: "any"` when the document type is unknown; forced tool to run a step first; nullable fields to stop fabrication; `"unclear"` and `"other"` + detail enums; normalization rules in the prompt.
  - 4.4 Validation-retry: resend document + failed extraction + specific errors; retries cannot recover information absent from the source; `detected_pattern` field; `calculated_total` vs `stated_total`, `conflict_detected`.
  - 4.5 Message Batches API: 50% cheaper, up to 24 h, no latency SLA, no multi-turn tool calling, `custom_id`; batch for overnight/weekly, sync for blocking pre-merge; submission-frequency math against an SLA; resubmit only failed items; refine the prompt on a sample first.
  - 4.6 Multi-instance review: independent instance beats self-review and extended thinking; per-file + integration passes; per-finding confidence for routing.
- **D5 Context Management & Reliability (15%):**
  - 5.1 Persistent "case facts" block outside summarized history; trim tool outputs to the relevant fields; lost-in-the-middle (key findings first, section headers); subagents return structured data with metadata.
  - 5.2 Escalation: explicit customer request (honour immediately), policy gaps/silence, no progress; not sentiment or self-reported confidence; multiple matches mean ask for more identifiers; offer resolution first when it is straightforward and the customer has not insisted.
  - 5.3 Error propagation: structured context (failure type, query, partial results, alternatives); neither silent empty success nor killing the whole workflow; coverage annotations in synthesis.
  - 5.4 Large codebases: context degradation, scratchpad files, subagent delegation, phase summaries, crash-recovery manifests, `/compact`.
  - 5.5 Human review: aggregate accuracy hides weak segments; stratified sampling of high-confidence output; field-level confidence calibrated on labeled sets; accuracy by document type and field before cutting review.
  - 5.6 Provenance: claim-source mappings kept through synthesis; annotate conflicting statistics with sources instead of picking one; publication dates; established vs contested sections; render each content type in its natural format.

## Reversals worth drilling

The single agent beats multi-agent; the plain prompt fix (better tool description, explicit criteria + few-shot) beats new infrastructure as the *first step*; the simple workflow beats the agent. But when compliance must be guaranteed (identity before refunds, refund limits), the hook or programmatic gate beats any prompt.

## Anti-pattern traps (make these the tempting wrong answers)

Taken from the guide's task statements and the wrong options in its sample questions:

- "Add it to the system prompt" or few-shot examples when a hook or programmatic prerequisite must guarantee the behaviour.
- Over-engineering as a first step: routing classifiers, keyword routers, separately trained models, consolidating tools, before fixing descriptions or prompt criteria.
- Escalating on sentiment or on the model's self-reported 1–10 confidence (calibrated field-level confidence on a labeled set is the valid version).
- Bigger model or bigger context window for attention dilution, instead of per-file passes or decomposition.
- Consensus voting across repeated runs (suppresses intermittently found real bugs).
- Blaming downstream subagents when the coordinator's decomposition was too narrow.
- Generic "operation failed" / "search unavailable" errors; empty results marked as success; terminating the whole workflow on one failure.
- Giving an agent every tool (over-provisioning) instead of a scoped cross-role tool.
- Loop control by parsing text, iteration caps, or "the assistant returned text" instead of `stop_reason`.
- Subagents assumed to see the coordinator's history.
- Free-text JSON parsing where `tool_use` + schema belongs; required fields that force fabrication.
- Retrying when the information is absent from the source, or retrying validation/permission errors as if transient.
- Batch API for blocking workflows; "batch with timeout fallback" complexity.
- Self-review in the generating session instead of an independent instance.
- Personal config (`~/.claude/...`, `~/.claude.json`) where shared project config is needed; or invented mechanisms (`CLAUDE_HEADLESS`, `--batch`, `.claude/config.json` commands array).
- Summarizing away the exact amounts, IDs and dates the task needs.

## Out of scope (guide section 17)

Do not write questions on: fine-tuning or training; API auth, billing, account management, OAuth, key rotation; language/framework internals beyond tool and schema config; hosting/deploying MCP servers; model internals, Constitutional AI, RLHF; embeddings and vector DBs; computer use; vision; streaming / SSE; rate limits, quotas, pricing calculations; cloud-provider config; benchmarks and model comparisons; prompt caching beyond knowing it exists; tokenization.

## Official resources

- CCAR-F Exam Guide v1.0 (July 2026): blueprint, task statements, scenarios, 12 sample questions, policies
- Anthropic Partner Academy prep path: anthropic-partners.skilljar.com
- Hands-on exercises from guide section 8: multi-tool agent with escalation; team Claude Code setup (CLAUDE.md, rules, skills, `.mcp.json`, plan mode); extraction pipeline with validation-retry and batches; multi-agent research pipeline with error propagation and provenance
- Claude Platform docs (tool use, structured outputs, Message Batches), Claude Code docs (CLAUDE.md, rules, skills, hooks, headless mode), Claude Agent SDK docs, MCP spec

Model names, prices and limits change. Learn the decision method, and check current values in the docs shortly before the exam.
