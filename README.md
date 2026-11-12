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
- **Sub-agent spawning** — heavy searches and reviews are delegated to isolated sub-agents with their own tool budgets.
- **Fix review panel** — proposed code changes appear in a UI panel; nothing lands without approval.

## Why "without knowing how to do it"?

Because the point isn't that a nurse can now write Python. The point is that **architecting, reviewing and steering an AI system is a different skill than typing code** — and this project is a long-running experiment in what that skill can produce. Every file here was written by AI, decided by a human, and survived daily production use.

## Status

Actively developed and in daily use. This repository currently contains documentation only (README, agent roster, architecture, codebase overview). Source code release is under consideration.

## Documentation

- [AGENTS.md](AGENTS.md) — the agent roster: who does what
- [ARCHITECTURE.md](ARCHITECTURE.md) — how the pieces fit together
- [CODEBASE.md](CODEBASE.md) — full module inventory, directory by directory


## Video of the harness (video from Sep. some stuff is missing in it)
https://www.youtube.com/watch?v=4RuOtlgRgog
