# The Agent Roster

Pantheon runs **8 agents + a system layer + a multi-model discussion panel**. Each agent has a distinct role, its own memory, and its own code module. None of them are "personas" in a prompt — they are separate processes with separate responsibilities.

| Agent | Role | One-liner |
|-------|------|-----------|
| **Thor** | Orchestrator / Boss Agent | Reads the user's message, decides: chat, task, or ask back. Plans, delegates, verifies, never blindly obeys. |
| **Ratatoskr** | Executor | Deterministic file operations, tool jobs, task execution — the hands of the system. |
| **Dvergar** | Prompt Enhancer | Turns short, slangy user input into structured, complete task descriptions before they hit the pipeline. |
| **Freya** | Passive Analyst & Memory | Watches in the background, extracts durable facts into semantic memory, reflects during idle time. Never blocks the main loop. |
| **Loki** | Media & Worker Hub | Routes work to ~20 specialized workers: video rendering, image generation, browser automation, YouTube, email, research, files, vision. |
| **Skadi** | Awareness & Skills | Detects repeated patterns and generates reusable skills from them. |
| **Heimdal** | Guardian | System scans, drift protection, dead-import detection, LOC alarms, code hygiene. |
| **Njörd** | Trading Agent | Paper trading on real market data with virtual money — autonomous decisions, Mon/Wed trading shifts, Saturday research, weekly plain-language report, own diary & source-learning. Live since 07 Nov 2026. |

## The System Layer

Not an agent, but the infrastructure all agents stand on:

- **Decision cascade** (3 stages) — decides autonomously when a situation is clear, escalates when it isn't
- **Tool governor** — controls which tool may be called when, with budget caps
- **Task memory** — persistent state across sessions
- **Event bus** — loose coupling between components
- **Spawn governor** — controls sub-agent creation and budgets

## Group Panel

Beyond the roster, the system includes a **Group Panel**: up to **7 different AI models** (different providers, different characters) discuss a single question together. Instead of one model's opinion, you get a debate — disagreements, corrections, and a synthesized conclusion. Useful for architecture decisions, reviewing fixes, and sanity-checking plans.

## Design principle

Agents are specialists, not replicas. Each one is bad at the others' jobs on purpose — because a system where everyone does everything is a system where nobody is accountable.
