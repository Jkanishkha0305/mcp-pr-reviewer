<div align="center">
  <h1>MCP Automation System</h1>
  <h3>Automated pull request review using Model Context Protocol, Gemini, GitHub, Slack, and Asana</h3>
</div>

## What This Repo Is

This repository demonstrates a full MCP-based automation workflow for pull request review. Instead of treating MCP as a toy protocol demo, it uses MCP the way an internal platform team would:

- expose real enterprise integrations as MCP servers
- aggregate those servers into a tool registry
- let a host application gather context across systems
- trigger the workflow from GitHub webhooks
- post actionable review summaries back to Slack

The current implementation focuses on one concrete use case: **PR review automation**.

## End-To-End Flow

```text
GitHub pull request opened
  -> webhook hits the MCP host
  -> host queries the MCP tool registry
  -> registry exposes GitHub / Slack / Asana / prompt servers
  -> Gemini synthesizes review context
  -> summary is posted into Slack
```

That gives you a realistic enterprise automation loop:
- GitHub provides the code and PR metadata
- Asana provides product or task context
- Slack becomes the delivery channel
- Gemini turns raw context into something reviewable by humans

## Why This Project Is Good Portfolio Material

- It is not "just an LLM wrapper"; it is an orchestration system.
- It uses MCP in a real systems integration setting.
- It shows platform thinking: tool registry, modular servers, host orchestration, webhook entrypoints, observability.
- It maps well to real internal developer tooling or AI automation platform work.

## Core Components

### `apps/pr-reviewer-mcp-servers`

This package contains the modular MCP servers:
- GitHub server for PR data and code-review context
- Slack server for message delivery and history access
- Asana server for task context
- prompt / scope utilities for reusable LLM workflow behavior
- tool registry aggregation layer

### `apps/pr-reviewer-mcp-host`

This package contains the orchestration layer:
- FastAPI webhook endpoint for GitHub PR events
- MCP host connection management
- Gemini-based review summarization
- Slack notification delivery
- Opik tracing hooks for observability

## Repository Layout

```text
mcp-automation-system/
├── apps/
│   ├── pr-reviewer-mcp-host/      # webhook-driven host/orchestrator
│   └── pr-reviewer-mcp-servers/   # GitHub, Slack, Asana, prompt servers + registry
├── LICENSE
└── README.md
```

## What A Demo Looks Like

1. Start the MCP server bundle and tool registry.
2. Start the host service.
3. Expose the host webhook via ngrok.
4. Open a pull request in GitHub.
5. Let the host gather repo diffs, task context, and prompt guidance.
6. Review the generated summary in Slack.

That is a much stronger story than "this repo contains some MCP experiments" because it shows the entire automation loop from event trigger to team-facing output.

## Quick Start

### Prerequisites

- Python `3.12`
- `uv`
- `make`
- Docker
- access to the relevant SaaS tokens for GitHub, Slack, Asana, Gemini, and optionally Opik

### 1. Start The MCP Servers

```bash
cd apps/pr-reviewer-mcp-servers
cp .env.example .env
uv venv .venv
. .venv/bin/activate
make install
make run
```

This starts the MCP server bundle and tool registry.

### 2. Start The Host

```bash
cd ../pr-reviewer-mcp-host
cp .env.example .env
uv venv .venv
. .venv/bin/activate
make install
make run
```

The host listens for webhook events on `http://localhost:5001`.

### 3. Expose The Webhook

```bash
ngrok http 5001
```

Add the generated public URL plus `/webhook` as your GitHub webhook target, then configure it for pull request events.

## Environment Overview

The system uses a mix of tokens depending on which side you are running:

### Host

- `GEMINI_API_KEY`
- `SLACK_CHANNEL_ID`
- `TOOL_REGISTRY_URL`
- `OPIK_API_KEY` (optional)

### Servers

- `GITHUB_ACCESS_TOKEN`
- `SLACK_BOT_TOKEN`
- `ASANA_ACCESS_TOKEN`
- `OPIK_API_KEY` (optional)

The detailed per-service setup still lives in the subproject READMEs.

## Where To Go Next

- [apps/pr-reviewer-mcp-host/README.md](apps/pr-reviewer-mcp-host/README.md)
- [apps/pr-reviewer-mcp-servers/README.md](apps/pr-reviewer-mcp-servers/README.md)

Use the host README when you want to run the webhook and orchestration layer.
Use the servers README when you want to configure integrations or the tool registry.

## What This README Fixes

The earlier top-level README described the idea, but it did not make the actual system flow obvious. This version makes the project legible in under a minute:
- what it is
- why it matters
- what runs where
- how the workflow is triggered
- why the architecture is interesting

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
