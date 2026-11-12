# LEARNING.md — The Learning Loop

The language model doesn't learn — the surrounding system does.

Thor's model weights are frozen. After every call, the model forgets everything. Yet the system behaves differently next week than it does today.

The trick is a **file-based, closed reality feedback loop**. "Fake" at the weight level. Real at the system level.

---

## 1 — The Episode Store (Reality Log)

Thor executes an action → **EpisodeStore.log()** writes an episode (`episodes.db`), measured with three metrics:

| Metric | Meaning |
|---|---|
| `execution_error` | Did the agent succeed? (`0.0` = success, `1.0` = failure) |
| `world_change` | State difference before and after the action |
| `confidence` | Simulator certainty (`0.0–1.0`) |

---

## 2 — Two Parallel Learning Paths (after every turn)

**TransitionMemory** — learns `State → Action → State`.
Condition: `frequency >= 3` → usable transition. Storage: `transition_memory.json`.

**Q-Memory** — learns `State → Action value`, with
`reward = 1.0 − world_change`. Storage: `q_memory.json`.

---

## 3 — Simulator: 3-Stage Cascade

| Stage | Mechanism | Requirement |
|---|---|---|
| 1 | TransitionMemory (historical) | `frequency >= 3`, `confidence >= 0.3` |
| 2 | RuleEngine | deterministic rules |
| 3 | LLM simulation (State + Action → JSON prediction; fallback `confidence = 0.3`) | expensive, last resort |

Output: `predicted_state + confidence` → written back into the episode log.

**DecisionLayer** (fast action filter): checks the Q-Score — `Q < 0.2` → action blocked. Guard activation only after 5 Q-Memory entries. Result: Thor selects better actions based on learned experience.

---

## 4 — Freya: The Persistent Memory System

Freya is the asynchronous learning mechanism. She produces no user-facing output and runs periodically in the background (trigger: during startup, if the last run was more than 3 days ago and enough new episodes are available).

**Input:** episodic raw data from `episodes.db`.

**Processing — three stages:**

- **Freya Light** — sequentially processes all unprocessed episodes, extracts observable facts (behavior patterns, preferences, workflows). Candidates go into a waiting room (`maybe_facts.json`).
- **Freya Idle Cognition** — selects ~12 random episodes, prioritized by high `prediction_error` (Thor was wrong — the primary learning source). Searches for hidden connections Light cannot detect.
- **Freya Reflection** — reviews waiting-room candidates. A candidate is only promoted after **3 independent confirmations from different episodes**. Evaluative, hypothetical, or one-time information is hard rejected.

**Output:** confirmed facts go to `decision_authority.py`, which decides:

- automatic integration into `semantic_memory.json` (if category rules and bootstrap thresholds are known), or
- `ALWAYS_PENDING` — categories `principle`, `architecture`: the user always makes the final decision manually, via the Pending Panel.

---

## 5 — The Memory Layers

| Layer | File | Writer | Decay |
|---|---|---|---|
| Core Memory | `core_memory.json` | Manual only | Never |
| Semantic Memory | `semantic_memory.json` | Freya only | Yes (§6) |
| Episodic Memory | `episodes.db` | Thor (automatic) | Never (archived) |
| Working Memory | session-transient | Thor (per task) | After session |

---

## 6 — Confidence Decay: Memory Quality Control

Decay is not a batch process — it executes lazily during every read/write access to a semantic memory entry. Every fact carries a `confidence` value (0.0–1.0) and a `last_seen` timestamp. If more than 7 days have passed since the last check, the category decay rate applies:

| Category | Decay / 30 days |
|---|---|
| `stefan_identity`, `loki_identity` | 0.000 (never) |
| `principle`, `architecture` | 0.000 (`ALWAYS_PENDING` — manual review) |
| `transferable_principle` | 0.010 |
| `preference` | 0.020 |
| `behavior_pattern` | 0.030 |
| `workflow` | 0.040 |

**Threshold:** if confidence falls below `0.35`, the fact is *not deleted* — it moves back into the waiting room (`count=1`) and can be re-confirmed. No hard deletion ever occurs.

Facts about rapidly changing areas automatically lose weight when no longer confirmed. Stable identity facts are protected. The system cleans itself without losing history.

---

## 7 — Why This Is "Real" Even Though It's "Fake"

- **Evidence-based:** the system stores meaning, never writing style or phrasing. Only information that appears independently three times becomes knowledge. One-off coincidences expire in the waiting room after 60–90 days.
- **Latency is a feature:** the system deliberately learns slowly — like a human who only internalizes a rule after repeated confirmation. This prevents overfitting to isolated events.
- **Ground truth:** claims about capabilities only count when backed by successful tool execution.

Learning does not happen inside the language model. It happens inside the loop:

```
experience → abstraction → memory → changed behavior
```

## The Complete Cycle

```
Action
  ↓
Reality
  ↓
Measurement
  ↓
Simulation
  ↓
Pattern Extraction
  ↓
Human Validation
  ↓
Memory Update
  ↓
Changed Future Behavior
```

Thor generates data → Freya extracts knowledge → Decay keeps knowledge current → Thor benefits during the next cycle. The loop is closed.
