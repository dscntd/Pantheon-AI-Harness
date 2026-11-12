# Architecture

## Big picture

```
                        ┌─────────────────────────┐
                        │      USER (desktop UI)  │
                        └───────────┬─────────────┘
                                    │
                        ┌───────────▼─────────────┐
                        │   THOR (Orchestrator)   │
                        │  chat / task / confirm  │
                        └───┬───────┬─────────┬───┘
                            │       │         │
              ┌─────────────▼─┐   ┌─▼──────┐ ┌▼──────────────┐
              │  DVERGAR      │   │SUB-    │ │ GROUP PANEL   │
              │  (prompt      │   │AGENTS  │ │ (up to 7 LLMs │
              │   enhancer)   │   │        │ │  in debate)   │
              └───────────────┘   └────────┘ └───────────────┘
                            │
        ┌───────────────────┼─────────────────────────┐
        │                   │                         │
┌───────▼───────┐  ┌────────▼────────┐  ┌─────────────▼───────────┐
│ RATATOSKR     │  │      LOKI       │  │     SYSTEM LAYER        │
│ (executor)    │  │  worker hub     │  │ decision cascade        │
└───────────────┘  │ ┌─────────────┐ │  │ tool governor           │
                   │ │ ~20 workers │ │  │ task memory / event bus │
                   │ │ video, sd,  │ │  │ spawn governor          │
                   │ │ browser,    │ │  └─────────────────────────┘
                   │ │ youtube,    │ │
                   │ │ email, ...  │ │
                   │ └─────────────┘ │
                   └─────────────────┘
        ┌───────────────────────────────────────────┐
        │         BACKGROUND LAYER                  │
        │  FREYA (memory/analysis)   SKADI (skills) │
        │  HEIMDAL (guardian)        NJÖRD (trading)│
        └───────────────────────────────────────────┘
```

## Core mechanisms

### CodeApply — supervised self-modification
The agents can change their own code, but never silently:
1. `prepare` — an exact byte-level copy of the target file goes to a fix workspace
2. the fix is applied **only to the copy**
3. a syntax/diff check runs automatically
4. the change appears in a **Review Panel** in the UI
5. a human approves → the change lands; otherwise it's discarded

### Self code graph
The system builds and maintains an index of its own codebase (~230 Python files, 500+ import edges). Agents navigate via symbol lookup (definition, callers, called symbols) instead of naive searching — this is what makes working in a large self-owned codebase feasible at all.

### Semantic memory
Freya extracts durable facts (user preferences, workflow rules, architecture decisions) with confidence scores. They are retrieved on demand and survive sessions. Notable constraint: the orchestrator deliberately has **no direct write access** to memory — memory mutation goes through analysis, not through a momentary whim.

### Decision cascade
Three escalating stages decide how much autonomy is appropriate for the current situation: routine situations run fully autonomously, ambiguous situations produce clarifying questions instead of guesses.

### Sub-agents
Heavy, isolated work (full-codebase scans, code review, research) is delegated to spawned sub-agents with their own tool profiles and call budgets. They see only their task text — no chat history — which keeps their reports unbiased and cheap.

## Scale

| Metric | Value |
|--------|-------|
| Python modules | ~230 |
| Import edges (indexed) | ~520 |
| Specialized workers | ~20 |
| Agent roster | 8 + system layer |
| Group Panel capacity | 7 concurrent LLMs |
| Semantic memory facts | 180+ |

*Numbers as of November 2026 — the system is under active daily development.*
