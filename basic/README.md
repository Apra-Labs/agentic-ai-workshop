# Agentic AI Workshop — Basic

**Build a complete React frontend, entirely agent-driven, in under two hours.**

This hands-on workshop teaches the core agentic AI workflow through four structured labs. You'll work on a real codebase with a real AI agent — not a toy demo. By the end you'll have a mental model that transfers to any project, any language, any agent.

---

## Recording

[![Watch the Agentic AI Workshop — Basic recording](https://img.youtube.com/vi/1CUNmxA4O1E/maxresdefault.jpg)](https://youtu.be/1CUNmxA4O1E?t=20)

*Full session recording — 10 Feb 2026*

---

## The Workflow You'll Learn

```
Onboard → Plan → Execute → Verify
```

This four-step pattern is the foundation of effective agentic development. You'll run through it once end-to-end, then recognise it every time you work with an agent.

| Step | What Happens | Why It Matters |
|------|-------------|----------------|
| **Onboard** | Agent explores the codebase and builds understanding | Faster and more accurate than manually briefing the agent |
| **Plan** | You co-design a phased roadmap | Prevents the agent from building the wrong thing |
| **Execute** | Agent writes code; you set guardrails and course-correct | You stay in control without doing the typing |
| **Verify** | Human-in-the-loop check: run tests, open the app, read the tests | Agents move fast — you confirm they moved in the right direction |

---

## What You'll Build

You'll add a React frontend to **NoteAPI** — a live Express/TypeScript REST backend with full CRUD, tag filtering, and search. The agent sets up the React app, builds the components, wires the API, handles loading and error states, and writes tests. You direct, review, and course-correct.

**Demo repository:** [github.com/Apra-Labs/agentic-workshop-demo](https://github.com/Apra-Labs/agentic-workshop-demo)
Clone the `basic_workshop_start` branch to follow along.

---

## Labs

### Lab 1 — Onboard (~10 min)
Point the agent at an unfamiliar codebase and let it explore. You'll see it accurately discover the API endpoints, data model, validation rules, and test coverage — without you explaining anything. Then you'll update `CLAUDE.md` together to add frontend context.

### Lab 2 — Plan (~10 min)
Give the agent your requirements, review its proposed plan, and push back on anything over-engineered. You'll negotiate error handling and loading states. The output is `plan.md` — a phased, testable roadmap.

### Lab 3 — Execute (~15 min)
Set guardrails (feature branch, commit milestones, don't touch the backend), then let the agent build Phase 1: React app scaffold, `NoteList` component, API integration, loading and error states. Course-correct when it over-engineers. Watch it commit at meaningful milestones.

### Lab 4 — Verify (~10 min)
Run the tests. Open the app. Create a note via `curl` and confirm it appears in the frontend. Read the test file — are the tests actually testing meaningful behaviour? If not, ask the agent to strengthen them. Document what was built in `progress.md`.

**Total time:** 45–55 minutes with Claude Code. The same work would take a full day without AI.

---

## Workshop Materials

| Content | Formats |
|---------|---------|
| Presentation (interactive) | [HTML](./agentic-ai-workshop-basic-2.0.html) · [PDF](./agentic-ai-workshop-basic-2.0.pdf) |
| Lab Guide | [Markdown](./lab-materials.md) · [PDF](./lab-materials.pdf) |
| Video Jockey Case Study | [Preface](./video-jockey-case-study-preface.pdf) · [Full](./video-jockey-case-study.pdf) |

---

## Prerequisites

- Node.js 18+ (`node --version` to check)
- Git installed and configured
- [Claude Code](https://claude.ai/code) installed: `npm install -g @anthropic-ai/claude-code`
- Basic comfort with the terminal
- Basic familiarity with TypeScript and React (you don't need to write it — the agent does)

The [lab guide](./lab-materials.md) covers all prerequisites. Complete the setup steps before the session — it takes about 10 minutes.

---

## Ready for More?

Once you've completed this workshop, the [Advanced Workshop](../advanced/) covers memory stacks, skills, MCP servers, safety hooks, the Ralph Wiggum Loop, overnight harnesses, and parallel subagents.
