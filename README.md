# Agentic AI Workshop

**Hands-on workshops for software developers learning to work effectively with AI agents.**

Developed and delivered by [Akhil Kumar](https://github.com/kumaakh), CEO & Co-founder, [Apra Labs](https://apralabs.com).

---

## Workshops

| | [Basic](./basic/) | [Advanced](./advanced/) |
|---|---|---|
| **Audience** | Developers new to agentic AI | Developers with hands-on agentic AI experience |
| **Duration** | ~2 hours | ~3 hours |
| **Labs** | 4 labs | 7 labs |
| **Focus** | The core agentic workflow: Onboard → Plan → Execute → Verify | Memory, skills, MCP, hooks, autonomous loops, overnight harnesses, parallel subagents |
| **You'll build** | A React frontend on a live REST API, entirely agent-driven | Production-grade agentic patterns on a real codebase |
| **Prerequisite** | None | Basic workshop + hands-on experience |

---

## Recordings

Both workshops were recorded live with ~20 developers attending each session. Full sessions — not highlights.

| Workshop | Recording | Date | Attendees |
|----------|-----------|------|-----------|
| **Basic** (~2 hrs) | [![Basic workshop recording](https://img.youtube.com/vi/1CUNmxA4O1E/maxresdefault.jpg)](https://youtu.be/1CUNmxA4O1E?t=20) | 10 Feb 2026 | ~20 developers |
| **Advanced** (~3 hrs) | [![Advanced workshop recording](https://img.youtube.com/vi/cDg0_0FTGE0/maxresdefault.jpg)](https://youtu.be/cDg0_0FTGE0) | 18 Feb 2026 | ~20 developers |

Direct links: [Basic](https://youtu.be/1CUNmxA4O1E?t=20) · [Advanced](https://youtu.be/cDg0_0FTGE0)

---

## Quick Start

Clone the demo project used in both workshops:

```bash
git clone https://github.com/Apra-Labs/agentic-workshop-demo.git
```

Then open the workshop folder for detailed setup instructions:

- [Basic Workshop →](./basic/)
- [Advanced Workshop →](./advanced/)

---

## What is Agentic AI?

An AI agent doesn't just answer questions — it reads your codebase, writes code, runs tests, commits changes, and course-corrects based on results. These workshops teach you the patterns and guardrails that make that powerful rather than dangerous:

- How to onboard an agent to an unfamiliar codebase
- How to plan collaboratively and set guardrails before execution
- How to verify agent output — because fast isn't the same as correct
- How to give agents bounded autonomy so they work while you sleep

---

## About Apra Labs

[Apra Labs](https://apralabs.com) is an edge-to-cloud specialist — video processing, machine learning, embedded AI, and computer vision since 2017. Our open frameworks like [ApraPipes](https://github.com/Apra-Labs/ApraPipes) give teams battle-tested production infrastructure to build on.

Agentic AI has transformed how we work across all of it. These workshops — and open tools like [apra-fleet](https://github.com/Apra-Labs/apra-fleet) — are our way of giving back: sharing what we've learned using AI agents on real, demanding production codebases so others can hit the ground running.

---

## Running These Workshops

The HTML presentations are self-contained interactive files with speaker notes, animated builds, and live code examples. **Download and open locally — do not use GitHub's file viewer.**

1. Download the HTML file (click → Raw → Save As) and open in Chrome or Firefox
2. Arrow keys or click to advance; press `S` for speaker notes
3. Share the lab guide with participants ahead of time so they complete setup before the session

**Basic** — participants need Node.js 18+, Git, and Claude Code installed. Ask them to clone the [`basic_workshop_start`](https://github.com/Apra-Labs/agentic-workshop-demo/tree/basic_workshop_start) branch of the demo repo.

**Advanced** — participants need Node.js 20+, Git, GitHub CLI, `jq`, and Claude Code. Ask them to fork [agentic-workshop-demo](https://github.com/Apra-Labs/agentic-workshop-demo) and enable GitHub Actions on their fork before the session. The [VS Code Workshop Profile](./advanced/vscode-workshop-profile.json) sets up the recommended screen-share layout.

---

## License

Workshop materials are © Apra Labs. Lab code and the demo project are open source — see individual repositories for license details.
