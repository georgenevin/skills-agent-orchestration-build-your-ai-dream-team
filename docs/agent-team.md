# Agent team

For Mona's Project Pulse dashboard, I will use a small custom team defined under `.github/agents/` and orchestrated with GitHub Copilot CLI in a Codespace. The setup keeps planning, design, coding, and delegation separate so the work can be executed in clear phases.

- Planner — Model: Claude Opus 5.5 (copilot). Responsible for researching the repository, checking relevant docs and dependencies, identifying edge cases, and producing an ordered implementation plan with file assignments and validation expectations. Definition: `.github/agents/planner.agent.md`
- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsible for coordinating the specialist agents, breaking work into parallel or sequential phases, assigning file scopes, and verifying that the final result fits together. Definition: `.github/agents/orchestrator.agent.md`
- Designer — Model: Gemini 3.7 Flash (copilot). Responsible for the product UX and visual design of the dashboard, including accessibility, information hierarchy, interaction flow, and the polished Project Pulse styling the app should present. Definition: `.github/agents/designer.agent.md`
- Coder — Model: GPT-5.5 (copilot). Responsible for implementing the assigned code and app support, including logic changes and any runnable app configuration required for the dashboard. Definition: `.github/agents/coder.agent.md`

This team is coordinated through GitHub Copilot CLI in a Codespace so the Orchestrator can delegate work to the Planner, Designer, and Coder while keeping each agent focused on a clear scope.
