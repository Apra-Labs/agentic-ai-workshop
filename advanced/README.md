# Agentic AI Workshop — Advanced

**Seven labs. Seven production patterns. The techniques that separate "I use AI sometimes" from "AI works while I sleep."**

This workshop is for developers who've already used an AI agent on real code and want to go deeper. You'll move beyond the basic workflow into the patterns that make agents reliable, safe, and genuinely autonomous — the techniques used on production codebases at Apra Labs.

**Prerequisite:** [Basic Workshop](../basic/) + hands-on agentic AI experience.

---

## Recording

[![Watch the Agentic AI Workshop — Advanced recording](https://img.youtube.com/vi/cDg0_0FTGE0/maxresdefault.jpg)](https://youtu.be/cDg0_0FTGE0)

*Full session recording — 18 Feb 2026*

---

## What You'll Learn

Seven labs, each targeting a specific gap between "agent that helps" and "agent that ships":

| Lab | Topic | The Problem It Solves |
|-----|-------|-----------------------|
| **1** | The Memory Stack | Every new session rediscovers your project from scratch |
| **2** | Skills & Subagents | Domain knowledge disappears when the conversation ends |
| **3** | MCP — Browser Snapshot | The agent can't see what a browser renders |
| **4** | Hooks That Bite | Common accidents happen because no guardrail caught them |
| **5** | The Ralph Wiggum Loop | You're the bottleneck — approving every step manually |
| **6** | The Overnight Harness | Tasks bigger than one context window stall |
| **7** | Subagents in Parallel | Sequential investigations bloat context and slow you down |

---

## Labs

### Lab 1 — The Memory Stack (~5 min)
The six-layer memory hierarchy: system prompt, `CLAUDE.md`, path-scoped rules, user memory, local memory, and auto-memory. You'll see auto-memory write to disk live, explore path-scoped rules that load only for matching files, and practice the **learning extraction habit** — turning every debugging struggle into a future shortcut.

### Lab 2 — Skills & Subagents (~5 min)
Skills package domain knowledge into markdown files that load on demand — progressive disclosure that keeps context lean. You'll explore the `noteapi-devops` skill, see how it knows what it does without loading the full body, then invoke it via a subagent. Your main context grows by only the summary — not the full investigation.

### Lab 3 — MCP: Browser Snapshot (~5 min)
MCP (Model Context Protocol) is USB-C for AI — one protocol, any tool, any agent. You'll configure the [browser-snapshot-agent](https://github.com/Apra-Labs/browser-snapshot-agent) MCP server, verify tools appear in `/mcp`, and watch Claude capture and analyse a screenshot in a single prompt. One tool call triggers browser access + visual analysis.

### Lab 4 — Hooks That Bite (~5 min)
**PreToolUse hooks** catch common accidents with a tiered response: hard block (`exit 2`) for irreversible commands, ask the user for commands that are risky-but-sometimes-intentional, allow silently for everything else. **PostToolUse hooks** automate quality — auto-linting fires after every file edit without you thinking about it. You'll trigger both tiers live and see why exit 2 fires even in full YOLO mode.

### Lab 5 — The Ralph Wiggum Loop (~5 min)
Four elements turn an enthusiastic agent into bounded autonomy: clear goal, binary success criteria, a completion promise, and a max-iterations safety cap. You'll install the official `ralph-loop` Claude Code plugin and watch it implement four TODOs, running tests after each, updating `progress.md`, and stopping cleanly when done.

### Lab 6 — The Overnight Harness (~5 min)
For tasks bigger than one context window. A shell loop restarts Claude with `claude -p "$(cat AGENT_PROMPT.md)"` — fresh context each cycle, state preserved in `plan.md`, `feature_list.json`, and `progress.md`. You'll run two cycles live, watch it pick up from where the last session left off, and read the morning trail: clean commits, updated state files, session-by-session progress notes.

### Lab 7 — Subagents in Parallel (~5 min)
Three investigations — security review, test coverage, TODO audit — dispatched simultaneously as subagents. Each runs in its own context window. Only condensed summaries return to the parent. You'll measure the context impact before and after: three full investigations, a fraction of the tokens.

---

## Workshop Materials

| Content | Formats |
|---------|---------|
| Presentation (interactive) | [HTML](./agentic-ai-workshop-advanced.html) · [PDF](./agentic-ai-workshop-advanced.pdf) |
| Lab Guide | [Markdown](./lab-materials.md) · [HTML](./lab-materials.html) |
| Pre-Workshop Setup | [PDF](./pre-advanced-workshop-setup.pdf) |
| VS Code Workshop Profile | [JSON](./vscode-workshop-profile.json) |

---

## Prerequisites

- **Basic workshop completed** — you should already know the Onboard → Plan → Execute → Verify loop
- Node.js 20+ (`node --version`)
- Git configured with GitHub auth (`gh auth login`)
- [Claude Code](https://claude.ai/code) latest version: `npm update -g @anthropic-ai/claude-code`
- `jq` installed (needed for Lab 4 hooks)
- GitHub CLI (`gh`) installed
- A forked copy of [agentic-workshop-demo](https://github.com/Apra-Labs/agentic-workshop-demo) with GitHub Actions enabled

Complete the [pre-workshop setup](./pre-advanced-workshop-setup.pdf) — it covers all of the above plus the Browser Snapshot MCP server install and VS Code workspace configuration. Budget 15–20 minutes.

---

## Referenced Repositories

| Repository | Used In |
|-----------|---------|
| [Apra-Labs/agentic-workshop-demo](https://github.com/Apra-Labs/agentic-workshop-demo) | All labs — fork this before the workshop |
| [Apra-Labs/browser-snapshot-agent](https://github.com/Apra-Labs/browser-snapshot-agent) | Lab 3 — MCP server for browser screenshots |
| [Apra-Labs/ApraPipes](https://github.com/Apra-Labs/ApraPipes) | Optional homework — a real production codebase with an advanced CLAUDE.md and multi-platform skill to browse |

---

## Key Ideas to Take Home

**Memory compounds.** Session 1 you struggle with 10 things. Session 20 you struggle with 1. Every line you add to `CLAUDE.md` prevents a future debugging session.

**Skills are institutional memory.** If you've had the same discovery conversation three times, extract it into a skill. The skill persists; the conversation doesn't.

**Exit 2 is absolute.** A PreToolUse hook that exits with code 2 blocks the command in every mode — including `--dangerously-skip-permissions`. Hard blocks for irreversible actions, confirmation prompts for risky-but-intentional ones.

**State files are the contract.** Human-readable plans for you, machine-readable checklists for the agent, progress logs for accountability. Without them, every restart is blind.

**Subagents keep your context clean.** The investigation cost stays in the subagent's context window. Only the summary comes back.
