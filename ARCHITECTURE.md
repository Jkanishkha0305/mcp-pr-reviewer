# Architecture — mcp-pr-reviewer

## MCP Workflow

```
  GitHub PR Event (webhook / manual trigger)
       │
       ▼
  ┌─────────────────────────────────────────────────┐
  │              MCP Orchestrator                   │
  │                                                 │
  │  Tool 1: GitHub MCP                            │
  │  └─ Fetch PR diff, file changes, comments      │
  │     Identify PR author, reviewers, labels       │
  │                                                 │
  │  Tool 2: Asana MCP                             │
  │  └─ Fetch linked task from PR description      │
  │     Get acceptance criteria, task context      │
  │                                                 │
  │  Tool 3: Code Analyzer                         │
  │  └─ LLM: analyze diff against Asana criteria   │
  │     • Does code implement what was planned?    │
  │     • Security issues?                         │
  │     • Performance concerns?                    │
  │     • Missing tests?                           │
  │                                                 │
  │  Tool 4: Slack MCP                             │
  │  └─ Post structured review to Slack channel    │
  │     • Summary + verdict (APPROVE / REQUEST)    │
  │     • Inline comments for key issues           │
  └─────────────────────────────────────────────────┘
```

## Configuration (.env)

```
GITHUB_TOKEN=          # GitHub personal access token
ASANA_TOKEN=           # Asana personal access token
SLACK_BOT_TOKEN=       # Slack bot OAuth token
SLACK_CHANNEL_ID=      # Channel to post reviews
OPENAI_API_KEY=        # LLM for code analysis
```
