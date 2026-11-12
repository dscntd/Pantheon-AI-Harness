# Pantheon AI Harness

A multi-agent AI system that runs as a desktop companion — 8 specialized agents, a persistent memory layer, and a supervised self-modification pipeline. Built incrementally over months, in daily use.

> Written by a full-time geriatric nurse who had never studied computer science. The AI agents wrote the code; a human steered, reviewed, and made the decisions. This repository documents what that collaboration produced.

## What is this?

Pantheon AI Harness is not a chatbot wrapper. It is an operating layer around local and cloud LLMs that turns them into a coordinated team:

- **Thor** — the orchestrator. Reads the user's intent, plans, delegates, verifies.
- **Ratatoskr** — the executor. Runs deterministic file operations and tool jobs.
- **Dvergar** — the prompt enhancer. Rewrites vague instructions into structured tasks.
- **Freya** — passive background analyst. Watches work sessions, distills durable facts into a semantic memory (~180+ facts), and reflects while idle.
- **Loki** — the media/worker hub. Coordinates ~20 specialized workers: video, image generation (Stable Diffusion), browser automation, YouTube, email, research, and more.
- **Skadi** — awareness and skill generation. Turns repeated solutions into reusable skills.
- **Heimdal** — the guardian. System scans, drift detection, LOC alarms, dead-import checks.
- **Njörd** — the trading agent. Paper trading on real market data with virtual money — autonomous decisions, Mon/Wed trading shifts, Saturday research, a weekly plain-language report, and its own trading diary. Live since 07 Nov 2026.

On top of that sits the **System layer**: decision cascade, tool governor, task memory, and an **event bus** — and the **Group Panel**, where up to 7 different AI models debate a question with each other before an answer is synthesized.

## Highlights

- **CodeApply pipeline** — every change to the system's own code goes through prepare → fix on a copy → syntax check → human review → commit. The agents can modify themselves, but a human signs off every change.
- **Self code graph** — the system indexes its own ~230 Python files and 500+ import edges, so agents can navigate their own codebase with symbol lookup instead of blind grep.
- **Semantic memory** — facts about the user and workflow are extracted, confidence-scored, and retrieved on demand. The system remembers across sessions.
- **Sub-agent spawning** — heavy searches and reviews are delegated to isolated sub-agents with their own tool budgets. (8 profiles : File, research, tester, editor etc.)
- **Fix review panel** — proposed code changes appear in a UI panel; nothing lands without approval.
- **Thor Overlay** — global hotkey (Ctrl+Alt+Space), always-on-top input bar. Talk to the agent from any app without switching to the UI. Chrome read access via CDP lets Thor see the page you're on (YouTube, Reddit, even a claude.ai chat).
- **Computer use** — Thor can see the screen and operate desktop apps himself.
- **MCP hub** — a connector tab with a catalog of public MCP servers (pin/unpin to make them permanent tiles). GitHub, Notion, Linear and Higgsfield (73 tools) are wired in. The agents can use any of them. They created this very repo through the GitHub MCP.
- **Local image generation** — Stable Diffusion via ComfyUI with Real-ESRGAN upscaling, plus a deterministic Pillow-based thumbnail renderer.
- **Videos as code** — agents write Remotion (React/TSX) compositions and render videos headlessly.
- **Routines** — daily and weekly scheduled tasks routed to specific agents.
- **Cost-aware model routing** — cheap and fast models for everyday work, strong ones only for hard agentic runs, local Ollama for simple execution tasks. Tool budgets per session keep costs predictable.
- **Thor Chess** — play a game against your orchestrator in split-screen while chatting with him on the side.
- **Autonomous YouTube channel manager** — Loki answers comments, uploads videos and handles the channel day to day, through the YouTube API.


## Status

Actively developed and in daily use. This repository currently contains documentation only (README, agent roster, architecture, codebase overview). Source code release is under consideration.

## Documentation

- [AGENTS.md](AGENTS.md) — the agent roster: who does what
- [ARCHITECTURE.md](ARCHITECTURE.md) — how the pieces fit together
- [CODEBASE.md](CODEBASE.md) — full module inventory, directory by directory
- [SYSTEM.md](SYSTEM.md) — the execution loop, tool governance and decision layer in depth
- [LEARNING.md](LEARNING.md) — the learning loop: from episodes to validated knowledge


## Video of the harness (video from Sep. some stuff is missing in it)
https://www.youtube.com/watch?v=4RuOtlgRgog
