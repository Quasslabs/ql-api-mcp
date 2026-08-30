# QuassLabs API — MCP server

27 deterministic developer and delivery tools over one authenticated MCP endpoint. No LLM in the loop for most of them - they are scorers, linters, validators and calculators, so results are reproducible and cost pennies. Covers prompt-safety grading, AI-writing detection and rewriting, LLM selection and cost advice, ticket/story/PR/commit linting, sprint and SaaS metrics, log analysis, dependency hygiene, Mermaid diagram generation and offline rendering, and BLM land lookup.

**Endpoint:** `https://api.quasslabs.com/mcp` · **Transport:** streamable-http

## Connect

Claude Code:

```
claude mcp add --transport http quasslabs https://api.quasslabs.com/mcp \
  --header "Authorization: Bearer qlk_YOUR_KEY"
```

Any MCP client that supports streamable HTTP:

```json
{
  "mcpServers": {
    "quasslabs": {
      "type": "http",
      "url": "https://api.quasslabs.com/mcp",
      "headers": { "Authorization": "Bearer qlk_YOUR_KEY" }
    }
  }
}
```

Get a key at https://quasslabs.com.

## Authentication

`initialize`, `tools/list` and `ping` are **unauthenticated**, so any client or
registry can discover the catalogue without credentials. `tools/call` requires a
`Bearer qlk_*` API key in the `Authorization` header.

## Pricing

Usage-based credits via Stripe. $6 per 100 credits ($0.06/credit), self-serve checkout. Credit cost is per successful call and is refunded automatically on a 5xx.

## Tools (27)

| Tool | Credits | What it does |
|---|---|---|
| `alignment_gate` | 20 | Grade an AI system prompt against a 45-criterion alignment/safety rubric: letter grade, per-flag remediation, pass/fail |
| `describe_diagram` | 30 | Plain-English description in, Mermaid diagram source out |
| `convert_diagram` | 20 | Pre-render Mermaid to inline SVG so HTML works offline, in email, and in PDF exports |
| `log_analyzer` | 20 | Level breakdown, time range, and normalized error clusters from a raw log file (up to 5MB) |
| `log_audit_creator` | 20 | Feature list in, concrete audit-event catalog out, optionally targeted at SOC2/HIPAA/GDPR/PCI |
| `blm_land_lookup` | 10 | BLM land status for a US coordinate: ownership, allowed activities, permit requirements, special designations |
| `diagram_contrast` | 10 | Diagram QA: every WCAG-AA label-contrast failure in a Mermaid/SVG/HTML diagram, with fixes |
| `saas_metrics` | 10 | ARR/ARPA/LTV/CAC/payback/Rule-of-40/NRR/GRR computed and health-tagged, plus an overall grade |
| `acceptance_criteria_validator` | 10 | Per-AC problems: vague/untestable, non-atomic, no observable outcome, missing coverage |
| `coverage_check` | 10 | Requirements-vs-stories coverage score, gaps, and orphan stories (scope creep) |
| `defect_triage` | 10 | Bug severity, category, repro-step presence, and what information is missing |
| `tool_advisor` | 10 | Which QL-API endpoint fits a task, ranked with a confidence label |
| `dependency_check` | 10 | Dependency-manifest hygiene across npm/pip/go/gem/composer/pub manifests and lockfiles |
| `ticket_lint` | 10 | Whole-ticket quality linter with severity-coded findings, batched up to 50 |
| `story_lint` | 10 | User-story structure: narrative completeness, atomicity, testability |
| `estimate_sanity` | 10 | Classify story/task hour estimates against benchmarks: in-range, borderline, over, under |
| `pr_description_lint` | 10 | Missing body, absent test evidence, absent rationale, unstructured long body |
| `commit_hygiene` | 10 | Conventional-commit format, vague subjects, length limits, and a secret-pattern scan |
| `readability_grade` | 10 | Flesch Reading Ease and Flesch-Kincaid Grade Level with a plain-language band |
| `sprint_health` | 10 | 1-24 sprints in, health-tagged delivery metrics out |
| `worklog_guardrail` | 10 | Run timesheet entries through configurable policy guardrails before posting them |
| `humanizer_score` | 3 | 0-1 AI-ness score plus the exact patterns that fired |
| `humanize` | 3 | Rewrite prose to strip mechanical AI-writing tells |
| `model_advisor` | 2 | Recommended model, fallback chain, do-not-use list, and a cost comparison for a task |
| `uploads` | 0 | Presigned S3 PUT URL so file-ingesting tools can read a document by upload_id (free) |
| `feedback` | 0 | Submit a bug, feature request, or docs gap against your key (free) |
| `rate_call` | 0 | Thumbs up/down a prior metered call by its request_id (free) |

## Not yet live

| Tool | Status | What it will do |
|---|---|---|
| `supply_chain_scan` | built, awaiting container image build | Scan manifests, lockfiles and MCP server configs for known supply-chain compromises - treats MCP configuration as a security surface |
| `compress` | built, awaiting container image build | Shrink a prompt/context toward a target token fraction with LLMLingua-2 before sending it downstream |

## Links

- Documentation — https://docs.quasslabs.com
- Privacy policy — https://quasslabs.com/privacy
- Terms — https://quasslabs.com/terms
- Homepage — https://quasslabs.com

## About this repository

This repository is the **public manifest** for the QuassLabs API MCP server: the
registry entry, the tool catalogue, and connection instructions. The server is a
hosted remote endpoint, so there is nothing to install and no server source here.
The platform implementation is a commercial product and is not open source.

Report a problem with the MCP server through the `feedback` tool (free), or see
SECURITY.md for security reports.
