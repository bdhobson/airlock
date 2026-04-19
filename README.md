# Airlock

A transparent inspection proxy for LLM API traffic and MCP tool call flows.

[![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL%203.0-blue.svg)](LICENSE)
[![Status: Early Development](https://img.shields.io/badge/status-early%20development-orange)]()
[![GitHub Discussions](https://img.shields.io/github/discussions/bdhobson/airlock)](https://github.com/bdhobson/airlock/discussions)

---

## Background

Prompt injection via indirect tool response is an underaddressed attack class in current agentic AI deployments. When an agent retrieves external content (web pages, documents, database records, API responses), that content enters the model's context window with no structural distinction from trusted system instructions. A sufficiently crafted payload embedded in retrieved content can redirect agent behavior, override system prompts, or trigger unintended tool calls.

This attack surface has been demonstrated in production systems at scale. Anthropic's Git MCP server received three CVEs in January 2026 ([CVE-2025-68143](https://www.cve.org/CVERecord?id=CVE-2025-68143), [CVE-2025-68144](https://www.cve.org/CVERecord?id=CVE-2025-68144), [CVE-2025-68145](https://www.cve.org/CVERecord?id=CVE-2025-68145)) for remote code execution achieved through prompt injection in tool responses. GitHub Copilot was assigned a CVSS 9.6 vulnerability in the same class. Microsoft 365 Copilot has been demonstrated susceptible to indirect injection through email and document content.

Existing mitigations target enterprise deployments with per-request pricing and managed onboarding. There is no open-source or self-hosted option for this threat class. Airlock is an attempt to fill that gap.

---

## Architecture

Airlock operates as a reverse proxy between the agent process and its upstream targets: the LLM API and, in later versions, MCP tool servers directly. All traffic passes through an inspection layer before forwarding.

```
                    ┌───────────────────────────┐
                    │         AIRLOCK           │
                    │                           │
Your Agent ────────►│  inspect inbound prompt   │────────► Anthropic API
                    │  inspect outbound reply   │◄──────── Anthropic API
                    │  inspect tool responses   │
                    │  enforce detection policy │────────► MCP Tool Servers
                    │  audit log                │◄──────── MCP Tool Servers
                    │                           │
                    └───────────────────────────┘
```

Integration requires a single environment variable change. No modifications to agent code are necessary.

```bash
# Standard configuration
ANTHROPIC_BASE_URL=https://api.anthropic.com

# With Airlock
ANTHROPIC_BASE_URL=http://localhost:4000
```

The proxy is transparent to the agent and API responses are structurally identical to direct Anthropic responses. Inspection and enforcement occur out-of-band from the agent's perspective.

---

## Detection scope

- **Indirect prompt injection** — adversarial instructions embedded in tool responses, retrieved documents, scraped content, or user-controlled input
- **System prompt override attempts** — known jailbreak templates, instruction suppression patterns, persona replacement attempts
- **Credential and filesystem exfiltration** — tool calls targeting sensitive paths (SSH keys, environment files, credential stores)
- **Tool misuse** — calls to tools outside expected behavioral scope, anomalous call frequency, unexpected sequencing
- **Behavioral deviation** — statistical divergence from baseline call patterns (configurable per agent)

---

## Enforcement model

Detection rules operate in one of two modes, configurable per rule:

**Monitor** (default) — traffic passes through; flagged events are logged and optionally forwarded to a webhook or Slack channel. Intended as the initial deployment mode to establish a behavioral baseline and validate rule accuracy before enabling enforcement.

**Block** — the request or response is rejected at the proxy layer before reaching the LLM or the agent. Rules with low false-positive risk (credential file access, exact-match jailbreak strings) default to block. Heuristic and pattern-based rules default to monitor.

Rule modes are adjustable through the dashboard based on observed traffic.

---

## Detection methodology

Detection does not rely on an external LLM service for evaluation, which would introduce latency, cost, and a secondary trust boundary. The detection pipeline uses local, layered heuristics:

1. **Static rule matching** — pattern matching against a maintained library of known injection templates, override syntax, and exfiltration signatures
2. **Embedding similarity** — cosine similarity against a local vector store of known attack patterns; no external embedding API calls
3. **Structural heuristics** — detection of unicode obfuscation, zero-width character insertion, and syntactic override patterns

The full pipeline runs locally. No request or response data leaves the host.

---

## Dashboard

A web dashboard provides:

- Real-time feed of proxied requests and responses
- Flagged event detail with the triggering rule and matched content
- Full audit log with exportable request/response payloads
- Per-rule mode configuration (monitor / block)

---

## Planned stack

- **Proxy:** Node.js as the transparent HTTP proxy, Anthropic API-compatible
- **Detection engine:** Rule engine + local embeddings
- **Dashboard:** React + lightweight API server
- **Deployment:** Docker Compose

---

## Status

Early development. The architecture and detection model are designed. Implementation is in progress.

**v1 scope:**
- [ ] LLM proxy
- [ ] Static rule engine + known injection pattern library
- [ ] Embedding-based similarity detection
- [ ] Web dashboard, real-time feed and flagged event detail
- [ ] Webhook and Slack alert delivery
- [ ] Docker Compose deployment

**v2 scope:**
- [ ] Native MCP protocol proxy (tool server traffic inspection)
- [ ] Expanded LLM / Agent compatibility
- [ ] Custom rule authoring
- [ ] Audit log export (CSV/JSON)
- [ ] Per-agent policy profiles

---

## Deployment

Airlock is designed for self-hosted deployment. No data leaves the host; inspection runs entirely in-process. A managed cloud option is planned for teams that prefer not to operate the proxy directly.

→ **[Airlock Cloud](https://airlock.co)** — managed deployment, coming soon.

---

## Community

This project is in early development. If you are building MCP-connected agents and have encountered prompt injection in the wild, or have thoughts on the detection model or enforcement design, contributions and discussion are welcome.

- ⭐ **Star this repo** to signal interest
- 💬 **[Open a Discussion](https://github.com/bdhobson/airlock/discussions)** to share your use case or threat model
- 👀 **Watch** for updates as development progresses

---

## License

AGPL-3.0. Free to use and self-host. See [LICENSE](LICENSE) for details.

---

*[@bdhobson](https://github.com/bdhobson) · [airlock.co](https://airlock.co)*
