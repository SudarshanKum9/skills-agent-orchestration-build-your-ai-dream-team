# Project Pulse dashboard implementation plan

## Goal

Create a polished, accessible static dashboard that lets contributors quickly see active projects, their owners, current status, recent activity, priority or risk, and a short summary. The app must open as the dashboard—not a directory listing—from the VS Code **Run Project Pulse Dashboard** configuration.

## Repository findings and constraints

- The project brief is `.github/project-pulse-brief.md`; it specifies the three app files, the project data fields, and the `app/` serving directory.
- `app/` currently has no files. There is no app framework, package manifest, or existing frontend style to extend, so keep this a dependency-free static app.
- `.vscode/tasks.json` already defines the exercise terminal task. Add the launch configuration separately in `.vscode/launch.json`; do not replace or edit the task.
- The custom-agent roles and workflow are documented in `.github/agents/` and `docs/agent-team.md`. The Designer should own visual/accessibility decisions, the Coder should implement the runnable app, and the Orchestrator should coordinate and verify.
- `.github/steps/3-step.md` and `.github/workflows/3-step.yml` specify expected dashboard and launch details. The workflow checks file existence, key phrases, and JSON syntax; it does not run the app.
- The repository has no app-specific test or build runner. `scripts/validate-exercise.sh` validates the exercise setup, including JSON syntax and required exercise files.

## Phases, ownership, and dependencies

### 1. Agree on the UI/data contract

- **Orchestrator:** Give Designer and Coder this shared contract before parallel work begins: a semantic dashboard wrapper with `.dashboard`, a project grid, `.project-card` per project, and stable hooks for status and priority badges. Project data uses a top-level `projects` array. Each record includes `name`, `owner`, `status`, `recentActivity`, and `priority`; include a short `summary` for the contributor-friendly description.
- **Designer:** Decide information hierarchy, accessible status/priority treatment, responsive behavior, typography, spacing, color, and card styling. Provide the agreed selectors and any design notes to Coder before integration. Do not edit Coder-owned files.
- **Coder:** Confirm the implementation can stay within the assigned files and use the agreed selectors and data shape.
- **Dependency:** This contract is a prerequisite for parallel CSS and markup work, avoiding conflicting assumptions about selectors and fields.

### 2. Build independent pieces in parallel

Once the contract is agreed:

- **Designer — `app/styles.css`:** Implement the visual system and responsive layout. Include `.dashboard` and `.project-card`, readable spacing, clear status and priority treatments, and polished card affordances such as `border-radius` and `box-shadow`. Include visible keyboard focus and sufficient contrast; ensure meaning is not conveyed by color alone.
- **Coder — `app/project-data.json`:** Add a valid top-level `projects` array with several illustrative projects. Every record must include all five required fields plus the short `summary`; use clear, consistent sample status and priority values.
- **Coder — `.vscode/launch.json`:** Add strict JSON for **Run Project Pulse Dashboard**. Serve from `${workspaceFolder}/app` with `python3 -m http.server 5500`; configure server readiness to open `http://localhost:%s/index.html`, rather than the app directory root. Do not modify `.vscode/tasks.json`.
- **Parallel decision:** These assignments can run in parallel after the UI/data contract because they have separate file ownership. The HTML integration is intentionally held until the selector and data contract are confirmed.

### 3. Implement and integrate the dashboard

- **Coder — `app/index.html`:** Create the page with the exact visible title **Project Pulse**, link `styles.css`, and load `project-data.json`. Render project cards using the agreed classes and display each required field plus summary. Use semantic headings and markup, and provide clear loading, empty, and data-load error states.
- **Dependency:** This follows Phase 1 and uses the JSON and CSS contracts. It may begin as soon as the shared contract is confirmed, but integration must verify the completed CSS and data files together.
- **Implementation note:** A small inline script in `index.html` can fetch and render the JSON without adding an unassigned JavaScript file or dependency. Run via the launch configuration/HTTP server; direct `file://` opening is not a supported way to load JSON with `fetch`.

### 4. Integrate and validate

- **Coder:** Check the four assigned files together, fix any mismatches in the assigned scope, and report results to Orchestrator.
- **Designer:** Review the rendered dashboard at desktop and narrow viewport widths, including readability, hierarchy, focus visibility, and status/priority clarity; report any CSS adjustments to Coder.
- **Orchestrator:** Confirm integration, launch behavior, and all validation expectations below. Keep any cross-owner adjustments coordinated to avoid simultaneous edits.
- **Dependency/sequencing:** Visual review requires a running integrated page, so it follows Phases 2–3. Fixes to `app/styles.css` and `app/index.html` should be coordinated; independent JSON and launch fixes may proceed in parallel if no file conflicts exist.

## File assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Accessible page structure, exact Project Pulse title, stylesheet/data references, dynamic card rendering, and loading/empty/error states. |
| `app/styles.css` | Designer | Visual system, responsive layout, card and badge styling, focus treatment, and contrast/readability. Coder integrates against the shared selectors and does not independently redesign this file. |
| `app/project-data.json` | Coder | Valid sample data with a top-level `projects` array; each item has `name`, `owner`, `status`, `recentActivity`, `priority`, and a concise `summary`. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named **Run Project Pulse Dashboard**, serving from `app/` and opening `index.html` at the server-ready URL. |

## Validation expectations

1. Confirm all four assigned files exist and inspect the rendered page; check that project cards—not a directory listing—appear.
2. Parse both JSON files with `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`.
3. Check that the HTML links `styles.css`, loads `project-data.json`, displays the exact title **Project Pulse**, and renders every project field and summary in cards. Check loading, empty, and fetch-error behavior.
4. Check the stylesheet contains `.dashboard` and `.project-card`, includes responsive rules and polished card treatment (`border-radius`, `box-shadow`), and remains readable and usable with keyboard focus and without relying on color alone.
5. Run **Run Project Pulse Dashboard** in VS Code. Confirm the server uses `app/` as its working directory and the browser opens `http://localhost:<port>/index.html`; check the output and URL if the server-ready pattern does not match the installed Python/VS Code environment. Stop the server after the smoke test.
6. Run `bash scripts/validate-exercise.sh` as a repository-level regression check. Its checks cover exercise wiring and expected file references; they do not substitute for the browser smoke test or visual review.
7. Confirm the Step 2 plan checks in `.github/workflows/2-step.yml` are satisfied: plan context, Designer/Coder roles, all four paths, dependencies, parallel work, and validation. After implementation, use `.github/workflows/3-step.yml` expectations as a completeness checklist; they check required phrases and JSON syntax but do not execute the app.

## Risks and open uncertainty

- The dashboard is static and has no established backend or product dataset. The sample projects and summaries will be illustrative, not live project information.
- The exact VS Code launch adapter/server-ready output can vary by VS Code version and environment. The specified command, working directory, launch name, and URL are explicit exercise requirements; verify the readiness pattern in the actual Codespace and adjust it if necessary while preserving those requirements.
- The brief requires a short summary but does not name a JSON key for it. This plan proposes `summary` as an additional field; confirm consistency during the shared contract step. The five required keys remain unchanged.
- No extra JavaScript file, build system, or dependency is currently warranted. Keep any implementation additions within the assigned scope unless integration demonstrates a concrete need and the Orchestrator approves it.
