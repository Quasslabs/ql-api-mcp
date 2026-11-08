# QuassLabs API — MCP server

32 deterministic developer and delivery tools over one authenticated MCP endpoint. No LLM in the loop for most of them: they are scorers, linters, validators and calculators, so results are reproducible and cheap. Covers prompt-safety grading, AI-writing detection and rewriting, LLM selection and cost advice, ticket/story/PR/commit linting, sprint and SaaS metrics, log analysis, dependency hygiene and supply-chain scanning, MCP tool linting, Mermaid diagram generation and offline rendering, and BLM land lookup.

**Endpoint:** `https://api.quasslabs.com/mcp` · **Transport:** streamable HTTP (stateless JSON-RPC 2.0) · **Server version:** 0.2.0

Documentation: https://docs.quasslabs.com

## Connect

Every client sends the same thing: a `Bearer qlk_...` API key in the
`Authorization` header. Hosts that reserve `Authorization` for themselves can
send `X-API-Key: qlk_...` instead.

**Claude Code**

```
claude mcp add --transport http quasslabs https://api.quasslabs.com/mcp \
  --header "Authorization: Bearer qlk_YOUR_KEY"
```

**Cursor** (`~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project)

```json
{
  "mcpServers": {
    "quasslabs": {
      "url": "https://api.quasslabs.com/mcp",
      "headers": { "Authorization": "Bearer qlk_YOUR_KEY" }
    }
  }
}
```

**Any other client that supports streamable HTTP**

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

Or call it directly:

```
curl -sS -X POST https://api.quasslabs.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer qlk_YOUR_KEY' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"account","arguments":{"action":"balance"}}}'
```

**Getting a key.** Sign up at https://quasslabs.com/api, or from inside an agent
with the `account` tool (`action: "signup"`, no key needed). Either way you get
an email with a one-time link, and the key is shown on quasslabs.com, never in
the agent transcript. Every key gets 300 free credits each month.

## Authentication

`initialize`, `tools/list` and `ping` are **unauthenticated**, so any client or
registry can discover the catalogue without credentials. `tools/call` requires
the API key, except the `account` tool's `signup` and `checkout_status`
actions. Keys are stored only as a SHA-256 hash, so a lost key cannot be
recovered, only revoked and reissued.

## Pricing

Prepaid credits, 1 credit = $0.01:

| Pack | Price | Credits |
|---|---|---|
| Starter | $5 | 500 |
| Pro | $20 | 2,500 |

Buy a pack from inside an agent with the `account` tool (`action: "buy_credits"`
returns a Stripe Checkout link for a human to pay), and check what is left with
`action: "balance"`. Each key's free monthly credits are spent before purchased
credits and refill on the first day of each UTC month.

Failed calls are not billed: a call rejected before the tool runs (bad input,
missing key, rate limit, insufficient credits) costs nothing, and a call that
fails on our side (any 5xx) is refunded automatically. That includes
`supply_chain_scan` archives that download but are rejected (not a ZIP, an
unsafe path, too many entries, or too large once extracted): the archive is
checked before anything is charged. A `convert_diagram` call whose diagrams
all fail to render (`422 mermaid_render_failed`) or whose output is over the
size limit (`413 output_too_large`) is refunded too.

## Tools (32)

Credits are per successful call. Tools marked 0 are free but still need a key.

### Writing and prompts

| Tool | Credits | What it does |
|---|---|---|
| `alignment_gate` | 35 | Grade an AI system prompt against a 45-criterion alignment/safety rubric: letter grade, per-flag remediation, pass/fail |
| `humanizer_score` | 0 | 0-1 AI-ness score plus the exact patterns that fired |
| `humanize_text` | 1 | Rewrite prose to strip mechanical AI-writing tells |
| `readability_grade` | 0 | Flesch Reading Ease and Flesch-Kincaid Grade Level with a plain-language band |

### Engineering and security

| Tool | Credits | What it does |
|---|---|---|
| `model_advisor` | 0 | Recommended model, fallback chain, do-not-use list, and a cost comparison for a task |
| `tool_advisor` | 0 | Which of these tools fits a task, ranked with a confidence label and the MCP tool name to call |
| `dependency_check` | 0 | Lint pasted dependency manifests for hygiene: pinning, lockfiles, insecure sources, install scripts, dependency confusion |
| `supply_chain_scan` | 5 | Scan a whole repo ZIP (manifests, lockfiles, MCP server configs) against a known-compromise catalog |
| `log_analyzer` | 5 | Level breakdown, time range, and normalized error clusters from a raw log file (up to 5MB) |
| `log_audit_creator` | 5 | Feature list in, concrete audit-event catalog out, optionally targeted at SOC2/HIPAA/GDPR/PCI |
| `regex_lint` | 1 | ReDoS risk analysis plus a plain-English explanation of a regular expression; compiles the pattern but never runs it |
| `mcp_tool_lint` | 1 | Lint MCP tool definitions for model usability: vague descriptions, untyped parameters, near-duplicates, missing annotations, tool-poisoning phrases |
| `openapi_diff` | 1 | Breaking-change report between two OpenAPI 3.x specs (JSON or YAML), with the semver bump it needs |

### Delivery and QA

| Tool | Credits | What it does |
|---|---|---|
| `ticket_lint` | 1 | Whole-ticket quality linter with severity-coded findings, batched up to 50 |
| `story_lint` | 1 | User-story structure: narrative completeness, atomicity, testability |
| `acceptance_criteria_validator` | 1 | Per-AC problems: vague/untestable, non-atomic, no observable outcome, missing coverage |
| `coverage_check` | 1 | Requirements-vs-stories coverage score, gaps, and orphan stories (scope creep) |
| `defect_triage` | 1 | Bug severity, category, repro-step presence, and what information is missing |
| `sprint_health` | 1 | 1-24 sprints in, health-tagged delivery metrics out |
| `worklog_guardrail` | 1 | Run timesheet entries through configurable policy guardrails before posting them |
| `estimate_sanity` | 1 | Classify story/task hour estimates against benchmarks: in-range, borderline, over, under |
| `pr_description_lint` | 1 | Missing body, absent test evidence, absent rationale, unstructured long body |
| `commit_hygiene` | 1 | Conventional-commit format, vague subjects, length limits, and a secret-pattern scan |

### Diagrams

| Tool | Credits | What it does |
|---|---|---|
| `describe_diagram` | 5 | Plain-English description in, Mermaid diagram source out |
| `convert_diagram` | 1 | Pre-render Mermaid to inline SVG so HTML works offline, in email, and in PDF exports |
| `diagram_contrast` | 0 | Diagram QA: every WCAG-AA label-contrast failure in a Mermaid/SVG/HTML diagram, with fixes |

### Data

| Tool | Credits | What it does |
|---|---|---|
| `saas_metrics` | 0 | ARR/ARPA/LTV/CAC/payback/Rule-of-40/NRR/GRR computed and health-tagged, plus an overall grade |
| `blm_land_lookup` | 8 | BLM land status for a US coordinate: ownership, allowed activities, permit requirements, special designations |

### Account and utilities

| Tool | Credits | What it does |
|---|---|---|
| `account` | 0 | One tool for your account. `action`: `signup` (email a one-time key link, no key needed), `balance` (credits, free credits left, reset date), `buy_credits` (Stripe Checkout link: $5 / 500 or $20 / 2,500), `checkout_status` (did a purchase land) |
| `create_upload` | 0 | Presigned S3 PUT URL so file-ingesting tools can read a document by upload_id |
| `send_feedback` | 0 | Submit a bug, idea, or docs gap against your key. Missing a tool? Send `kind: "tool_request"` |
| `rate_call` | 0 | Thumbs up/down a prior metered call by its request_id |

`tools/list` only ever advertises what is callable right now. `compress`
(LLMLingua-2 prompt compression, 10 credits) is built but temporarily switched
off, so it is not listed.

### Renamed in 0.2.0

`humanize` is now `humanize_text`, `uploads` is now `create_upload`, and
`feedback` is now `send_feedback`. The REST endpoints (`/v1/humanize`,
`/v1/uploads`, `/v1/feedback`) did not change.

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

Report a problem with the MCP server through the `send_feedback` tool (free), or
see SECURITY.md for security reports.
