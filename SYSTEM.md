# SYSTEM.md — The Execution System

How a task travels through Pantheon: from natural-language input to executed, measured, self-improving action.

---

## 1 — Thor: The Only Interface

One conversation partner instead of forty triggers.

The user talks to exactly one instance: **Thor** (You can talk to each agent, but u dont have to). Thor understands *intent*, not keywords — no `create video:` colon-commands, just natural language. Behind the scenes, Thor runs an agent loop:

```
understand → call tool → inspect result → think → call next tool → respond
```

Thor serves four roles:

| Role | Meaning |
|---|---|
| **Translator** | Converts user intent into existing capabilities. A capability's purpose is defined when it is used, not when it is built. |
| **Composer** | Chains and branches capabilities at runtime ("if 3 of 5 emails are about topic X → summarize + save"). The logic exists *between* tool calls, not inside fixed pipelines. |
| **Single Presenter** | Everything the system communicates passes through Thor's voice. Raw system messages never reach the user. |
| **Economist** | Uses native tools first, and only delegates to Loki as the final (and most expensive) fallback. An explicit user instruction always overrides the cost policy. |

Thor is not limited by *trust*, but by *code*:

- **Approval Gate** — system code may only be modified with the user's explicit approval. UI content is Thor's playground.
- **Governor** — protects against excessive cost and execution volume.

Behavioral rules in the prompt express intent. Code-level checks provide the safety net.

---

## 2 — Startup & Context

Model: **GLM 5.3 Flash** via OpenRouter. All other subsystems are backend components — the user interacts exclusively with Thor.

At startup, Thor loads:

- Core Memory (always)
- Top-K Semantic Memory facts (`k=10` default, `k=25` during tool calls)
- Active project knowledge
- Relevant episodes via retrieval when needed

The context stays controlled — no "dump everything" approach.

---

## 3 — Execution Loop (per task)

```
User Input
    ↓
Context Compiler → Core + Semantic + Episodic (Top-K)
    ↓
LLM Call → Generate Plan
    ↓
DecisionLayer → simulate(action) + evaluate(predicted_state)
    ↓
ToolGovernor → Budget Check + Loop Detection + Error Classification
    ↓
Tool Execution → deterministic execution
    ↓
State Snapshot (before/after)
    ↓
Write Episode into episodes.db
    ↓
FixReviewPanel → user approved → Git Auto-Snapshot
```

---

## 4 — ToolGovernor (v3)

No frequency-based blocking. Instead:

**Budget pool:**
- Normal mode: 150 points
- Explore mode: 300 points

**Loop detection** based on output *difference*, not tool frequency.

**Error classification:**

| Error type | Cost | Rationale |
|---|---|---|
| `MODEL_JSON_ERROR` / `TIMEOUT` / `NETWORK` | 0.0 | Not the agent's fault |
| `TOOL_EXCEPTION` | 1.0 | Charged |

Cooldown activates only when `fail_score ≥ 2.0`, and is temporary with event-based reactivation.

---

## 5 — DecisionLayer

Before every action, the layer simulates the expected system state (`predicted_state`). After execution, the actual state is compared.

The delta (`prediction_error`) becomes the learning source (see [LEARNING.md](LEARNING.md)):

> A high `prediction_error` combined with zero `execution_error` means: the tool execution was correct, but the conceptual prediction was wrong. These cases enter the jackpot filter for Idle Cognition.

---

## 6 — Delegation

Thor can delegate tasks to:

- **Claude** (via Anthropic API) — heavy analysis/review work
- **Loki** — media worker with 40+ specialized sub-workers

Thor waits for the result and integrates it into his own context. For the user, Thor remains the only conversational interface.

What delegation changed: individual capabilities (email, timers, browser automation, Stable Diffusion, Remotion, vision, 40+ workers) sat isolated behind their own keyword bottlenecks for months. Delegation connected the final island — every feature instantly became more valuable *without being modified*, because every possible combination now exists implicitly.

Workers are intentionally simple and deterministic — they are sensors and actuators. **The intelligence lives in the orchestrator.**

---

## 7 — The Architecture in One Line

```
User
   ↓
Thor        — understands • composes • presents
   ↓
Native tools (cheap, first choice)
   ↓
Loki + Workers (powerful, last resort)

Freya       — learns from everything during idle time
Approval Gates + Governor — code-level safety net

User
   • outside the system
   • the only unfalsifiable authority
```

One conversation partner. One catalog of capabilities. One learning loop. One safety net.
