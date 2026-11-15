# Codebase Overview

A directory-by-directory inventory of the system. This is the full map — roughly 230 Python modules plus UI assets. The system is in daily use; this document reflects its state as of **November 2026**.

> Note: the agents built this for themselves. Some of it is over-engineered by classic standards — deliberately so, since parts of the system exist to govern other parts.

---

## Root (entry points & services)

| Module | Role |
|--------|------|
| `main.py` | Entry point — boots the whole system |
| `mcp_manager.py` | MCP (Model Context Protocol) server management |
| `calendar_service.py` | Calendar backend (SQLite) |
| `r08_metrics.py` | Metrics collection |
| `restore_last.py` | Rollback utility for the CodeApply pipeline |
| Start scripts (`.bat`) | One-click launchers for the full stack |

## `core/` — The Brain (~53 modules)

The infrastructure every agent stands on. Named below are the core load-bearing modules; the full set is ~53.

- **LLM layer:** `llm_client`, `llm_router`, `config` — multi-provider model access with routing (local and cloud models mixed)
- **Memory:** `task_memory`, `memory_core`, `memory_manager`, `agent2_memory`, `q_memory`, `transition_memory` — layered persistent memory
- **Decision:** `decision_layer`, `simulator` (3-stage decision cascade, live), `canonical_state`
- **Governance:** `tool_governor` (which tool may run when, with budgets), `spawn_governor`, `gpu_arbiter` (GPU contention between workers)
- **Sub-agents:** `subagent_bridge` — the spawn factory with tool profiles and call budgets
- **Code safety:** `code_apply`, `patch_manager`, `fix_classifier`, `execution_contract` — the supervised self-modification pipeline
- **Self-navigation:** `explore_symbol` support, capability store, prompt builder
- **Support:** `logger`, `event_bus`, `abort_guard`, `error_metrics`, `feedback_analyst`, `gap_detector` (including a proactive variant), `eval_classifier`/`eval_store` with rules file, `scene_binding`, `vision_processor`, YouTube auth/setup, and more

## `orchestrator/` — The Pipeline (~25 modules)

Everything between "user intent" and "worker gets a job". Named below are the main pipeline modules; the full set is ~25.

- **Planning:** `loki_planner`, `planner`, `plan_builder`, `execution_plan`, `templates`
- **Loop & routing:** `agent_loop`, `loop_engine`, `router`, `scheduler`, `intent_resolver`
- **Specialized orchestrators:** `media_orchestrator`, `insta_orchestrator`, `mini_orchestrator_base`, `orchestrator_registry`
- **Analysis:** `output_classifier`, `gap_analysis`, `tool_registry`, `prompt_builder`, `caption_builder`, `scene_builder`, `scene_policy`

## `workers/` — The Hands (~20 modules)

One module per capability, each replaceable in isolation. All top-level modules (subfolders excluded):

- **Media:** `remotion_worker` (programmatic video), `thumbnail_worker`, `sd_worker` (Stable Diffusion), `pexels_video_researcher`, `pexels_worker`, `video_cutter_worker`, `video_editor_worker`, `loki_image_generator`, `loki_scene_picker`
- **Interaction:** `browser_worker` (automated browsing), `email_worker`, `notepad_worker`, `music_player`
- **Knowledge:** `research_worker`, `code_worker`, `vision_worker`, `music_query_resolver`, `youtube_worker`
- **Foundation:** `file_worker`, `base_worker`

## `thor/` — The Orchestrator Agent (~11 modules)

- `thor_agent.py` — main agent, system prompt, tool dispatch
- `thor_tools.py` — tool implementations (git, browser, delegation, …)
- `git_tools.py` — git snapshots, history, diffs
- `tool_router.py`, `base_agent.py`, `explore_symbol.py` — code navigation and routing
- `browser_tools_neu.py`, `chrome_cdp_tools.py` — browser automation and Chrome CDP access
- `agent_profiles/` — agent configuration profiles
- **Memory:** `episode_store` (SQLite), `semantic_memory`, `thor_memory_init`

## `freya/` — The Passive Analyst (~9 modules)

- `freya_core`, `freya_reflection` — analysis and reflection loop
- `freya_light` — lightweight mode
- `freya_status` — status reporting
- `idle_cognition` — thinking during downtime
- `decision_authority` — what Freya may decide alone
- `heartbeat` — scheduled background wake-ups
- `proactive_queue` — queue for proactive suggestions
- `moltbook_watcher` — file-based event watcher

## `ui/` — The Face

All top-level modules (assets, avatars and the `pantheon_split/` subfolder excluded; `.bak_*` files are rollback snapshots):

- `workspace_window_v3.py`, `workspace_window.py` — the live workspace UI and its legacy dev console
- `robot_window.py`, `thor_panel.py`, `thor_overlay.py` — main windows and the global Thor overlay
- `Pantheon_AI_v2.html` — the frontend for the live UI (modules in `pantheon_split/`)
- `fix_review_panel.py` — CodeApply fix review UI
- `freya_pending_panel.py`, `insta_panel.py`, `interactive_office.py` — specialized panels
- `calendar_tab.py`, `message_scheduler.py` — calendar and routines
- `desktop_pet.py`, `speech_bubble.py`, `loki_spoken_overlay.py` — avatar and speech UI
- `r08_theme.py`, `setup_dialog.py`, `open_old_workspace.py`, `export_cortex_data.py` — theming and utilities

---

## Reading tip

If you want to understand the system, read in this order:
1. `core/decision_layer.py` — how autonomy is granted and escalated
2. `core/tool_governor.py` — how agent power is constrained
3. `core/code_apply.py` + `patch_manager.py` — how the system safely edits itself
4. `thor/thor_agent.py` — the main loop
5. `orchestrator/pipeline.py` — how a task becomes actions

*(Source code is not published in this repository yet — this overview documents the structure.)*
