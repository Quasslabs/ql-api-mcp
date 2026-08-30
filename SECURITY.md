# Security

## Reporting a vulnerability

Email **contact@quasslabs.com** with "SECURITY" in the subject. Include the
affected endpoint or tool, what you observed, and the steps to reproduce it.
Please do not open a public issue for a security report.

We aim to acknowledge within three business days.

## Scope

In scope: the hosted MCP endpoint at `https://api.quasslabs.com/mcp`, its
authentication and metering behaviour, and anything published in this
repository.

Out of scope: findings that require a compromised client machine, denial of
service through ordinary rate limits, and reports generated solely by an
automated scanner with no demonstrated impact.

## Please do not

Run load or denial-of-service tests against the live endpoint, or attempt to
access another tenant's data. If you need a test key to investigate something,
ask first.

## Authentication model

`initialize`, `tools/list` and `ping` are intentionally unauthenticated so that
clients and registries can discover the tool catalogue without credentials.
`tools/call` requires a `Bearer qlk_*` API key. A call made without a valid key
is refused and the tool does not execute.
