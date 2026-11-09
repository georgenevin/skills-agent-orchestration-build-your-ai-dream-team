# Project Pulse implementation plan

## Summary

Build Mona’s **Project Pulse** as a small static dashboard: semantic HTML, responsive CSS, and local JSON data, served from `app/` in VS Code. The repository has an `app/` directory but no dashboard files yet; there is no existing frontend framework, build system, or test suite to extend. Keep the implementation dependency-free and limited to the four requested files.

The repository brief and exercise checks require the exact dashboard title, project cards that show each project’s owner, status, recent activity, and priority, and a launch configuration named **Run Project Pulse Dashboard**. The brief also asks for a contributor-friendly summary, which is not among the required JSON fields; this plan treats `summary` as an optional additional field unless Mona confirms otherwise.

## Ordered implementation steps

| Step | Work and owner | File assignments | Dependencies |
|---|---|---|---|
| 1. Agree on the UI/data contract | **Designer** defines the information hierarchy, accessible card structure, visual treatment, responsive behavior, and the CSS hooks the HTML will use. **Orchestrator** confirms the contract with Coder before implementation. | No production files. Designer hands off the contract to Coder. | First. The contract prevents mismatched markup and styling. |
| 2. Build the independent foundation in parallel | **Designer** implements the agreed responsive styles. **Coder** creates representative data and the VS Code preview configuration. Keep each agent within the assigned files. | Designer: `app/styles.css`.<br>Coder: `app/project-data.json`, `.vscode/launch.json`. | After Step 1. CSS hooks and required data keys are fixed. The launch settings can be authored independently, but must be integration-tested later. |
| 3. Implement and connect the page | **Coder** creates the semantic page, loads the JSON, renders the cards, and connects the agreed styles and data. | Coder: `app/index.html`. | After Step 2: it must use the CSS hooks from `app/styles.css` and read the schema in `app/project-data.json`. |
| 4. Integrate and validate | **Coder** fixes implementation issues within the assigned files. **Designer** reviews the visual and accessibility result and reports recommendations; **Orchestrator** verifies the complete app and launch flow. | Existing four files only: `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`. | After Steps 2–3. Validate the running app, not only file contents. |

### File ownership and responsibilities

- **Designer — `app/styles.css`:** Own visual design, hierarchy, responsive layout, readable spacing, contrast, status/priority treatment, and CSS hooks such as `.dashboard` and `.project-card`. Review the rendered result for accessibility and consistency; do not edit the HTML, JSON, or launch file.
- **Coder — `app/index.html`:** Own semantic markup and the integration between the page, stylesheet, and JSON. Use the exact title **Project Pulse** and render visible cards with the required project fields.
- **Coder — `app/project-data.json`:** Own valid JSON with a top-level `projects` array. Every project must include `name`, `owner`, `status`, `recentActivity`, and `priority`. Consider adding `summary` to meet the brief’s contributor-friendly-summary goal.
- **Coder — `.vscode/launch.json`:** Own strict JSON for the **Run Project Pulse Dashboard** configuration. Follow the exercise’s required serving command, working directory, and page URL described below.
- **Orchestrator:** Assign the file boundaries, relay Designer’s agreed UI contract to Coder, resolve blockers, and validate the integrated app. No production-file ownership.

## Dependencies and parallel work

- **Sequential:** Agree on the UI/data contract first; implement `app/index.html` only after the CSS hooks and JSON fields are clear; run the final preview after all four files exist.
- **Can run in parallel after Step 1:** Designer’s CSS, Coder’s JSON data, and Coder’s launch configuration. These files have distinct owners and their initial contents do not depend on one another.
- **Must be integrated:** The HTML must use Designer’s classes and render Coder’s data fields. The launch configuration must serve the completed page, not merely start a server.
- Keep ownership exclusive during implementation. If the review discovers a mismatch, route the change to the owner of that file rather than having agents edit the same file concurrently.

## Edge cases and risks

- Handle JSON fetch failures, non-success HTTP responses, invalid JSON, and an empty `projects` array with a clear page-level message; do not leave a blank dashboard.
- Keep field names consistent with the brief (`recentActivity`, not a renamed variant). Decide whether the contributor-friendly summary is an extra `summary` field or whether `recentActivity` is intended to fulfill that need.
- Use readable text labels in addition to color for status and priority. Allow long project names, activity text, and summaries to wrap without breaking the card layout.
- Serve the page over HTTP: browser `fetch()` of the local JSON should not be assumed to work when opening the HTML directly via `file://`.
- The exercise specifies `python3 -m http.server 5500`, a working directory of `${workspaceFolder}/app`, and opening `http://localhost:%s/index.html` using `serverReadyAction`. Confirm that the selected VS Code launch type supports this command in the Codespace. The devcontainer lists Copilot and GitHub Actions extensions, but not the Python debugger; do not assume a Python-specific debug configuration will be available without checking.
- The Python standard-library `http.server` is suitable for this local preview, not production hosting.

## Validation expectations

1. **Static checks:** Confirm the four assigned files exist; `app/index.html` includes “Project Pulse” and references `styles.css` and `project-data.json`; it renders `.project-card` elements and displays the required fields.
2. **Data and configuration:** Parse both JSON files with `python3 -m json.tool`. Confirm `project-data.json` has a top-level `projects` array and every entry has the five required fields. Confirm launch name, `index.html` target, app working directory, server command, and browser URL match the exercise requirements.
3. **Visual/accessibility review:** Inspect the dashboard at desktop and narrow viewport widths. Check readable spacing, text wrapping, sufficient contrast, and that statuses/priorities are understandable without color alone.
4. **Runtime check:** Run **Run Project Pulse Dashboard** in VS Code. Verify it opens the dashboard at `/index.html`, displays cards from the JSON rather than a directory listing, and handles empty or unavailable data clearly. Check the terminal for missing-file, port-in-use, or launch-type errors.
5. **Exercise grading:** The Step 3 workflow checks the required file paths, content keywords, JSON parsing, and launch name/target. The Step 2 workflow checks that the saved plan includes file assignments, Designer and Coder responsibilities, dependencies, parallel work, and validation.

## Documentation and repository research

- The requirements are defined in `.github/project-pulse-brief.md` and `.github/steps/3-step.md`; grading expectations are in `.github/workflows/3-step.yml`.
- The VS Code [debug configuration documentation](https://code.visualstudio.com/docs/debugtest/debugging-configuration) describes workspace `launch.json` files and `serverReadyAction`; debugger-specific launch properties depend on the selected debugger.
- The Python [`http.server` documentation](https://docs.python.org/3/library/http.server.html) describes the standard-library server and cautions that it is not intended for production.
- MDN’s [Fetch API guide](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) explains checking `Response.ok` and handling fetch/JSON errors.

## Open questions

1. Should each project have an additional `summary` field, or should `recentActivity` also serve as the brief summary?
2. Which launch configuration type is available in the target Codespace to run the required Python server and trigger `serverReadyAction` without adding an extension dependency?
3. Which exact status and priority values should the sample data use? The brief does not define allowed values; choose a small, consistent set and keep labels human-readable.
