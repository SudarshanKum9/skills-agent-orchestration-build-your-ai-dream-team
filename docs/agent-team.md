# Agent team for Mona's Project Pulse dashboard

We will use a coordinated custom agent team in GitHub Copilot CLI from a Codespace to plan, design, and implement the Project Pulse dashboard.

- Orchestrator — `Claude Opus 5.5 (copilot)` — coordinates the workflow, breaks the work into phases, assigns file scope, and delegates to specialist agents while verifying the integrated result. Definition: `.github/agents/orchestrator.agent.md`.
- Planner — `Claude Opus 5.5 (copilot)` — researches the repo, checks dependencies, identifies edge cases, and produces an actionable implementation plan without writing code. Definition: `.github/agents/planner.agent.md`.
- Coder — `GPT-5.5 (copilot)` — writes and fixes implementation code, keeps the app behavior deterministic and testable, and handles the runnable app support needed for the dashboard. Definition: `.github/agents/coder.agent.md`.
- Designer — `Gemini 3.7 Flash (copilot)` — focuses on UI/UX, accessibility, information hierarchy, visual polish, and responsive dashboard styling for Project Pulse. Definition: `.github/agents/designer.agent.md`.

This custom team lives under the repository's agent folder: `.github/agents/`. Using GitHub Copilot CLI in a Codespace lets us orchestrate the planning, design, and implementation work across the team in a structured, parallel-ready workflow.
