# CCAR-F: Claude Certified Architect – Foundations

Source: official "Claude Certified Architect – Foundations Exam Guide", Version 1.0, effective July 2026 (Anthropic Partner Academy, certifications page). The official guide wins over this file.

## Facts

| Field | Value |
| --- | --- |
| id | ccar-f |
| name | Claude Certified Architect – Foundations |
| exam_questions | 60 |
| exam_minutes | 120 |
| score_min | 100 |
| score_max | 1000 |
| pass_mark | 720 |
| pass_rule | Pass/fail uses the total scaled score only; per-domain percentages are informational |

Item format: multiple-choice and multiple-response; each item states how many responses to select.

Exam structure: 4 scenarios, picked at random from the 6 below; each scenario frames a set of questions.

Other facts (guide sections 3, 12, 15): fee $125 USD; delivered by Pearson VUE (online proctored or test center); credential valid 12 months, renewed by a free non-proctored assessment; retake waits 14 / 30 / 90 days, max 4 attempts per rolling 12 months.

## Domains

Weights must add up to 100%.

| ID | Name | Weight |
| --- | --- | --- |
| D1 | Agentic Architecture & Orchestration | 27% |
| D2 | Tool Design & MCP Integration | 18% |
| D3 | Claude Code Configuration & Workflows | 20% |
| D4 | Prompt Engineering & Structured Output | 20% |
| D5 | Context Management & Reliability | 15% |

## Scenarios

The guide's six scenarios (section 5), with their primary domains:

- Customer Support Resolution Agent: Agent SDK agent with MCP tools `get_customer`, `lookup_order`, `process_refund`, `escalate_to_human`; target 80%+ first-contact resolution (D1, D2, D5)
- Code Generation with Claude Code: slash commands, CLAUDE.md, plan mode vs direct execution (D3, D5)
- Multi-Agent Research System: Agent SDK coordinator with search, document-analysis, synthesis and report subagents; cited reports (D1, D2, D5)
- Developer Productivity with Claude: Agent SDK agent using built-in tools (Read, Write, Bash, Grep, Glob) plus MCP servers (D2, D3, D1)
- Claude Code for Continuous Integration: automated reviews, test generation, PR feedback; actionable feedback with few false positives (D3, D4)
- Structured Data Extraction: extraction from unstructured documents, JSON-schema validation, edge cases, downstream integration (D4, D5)
