# Lab Materials — Agentic AI Workshop (Advanced)

**Workshop:** Agentic AI for Software Development — Advanced
**Author:** Akhil Kumar, CEO & Co-founder, Apra Labs
**Prerequisite:** Basic workshop + hands-on agentic AI experience

These labs accompany the live demos in the workshop. Each lab gives you exact steps to follow along or try on your own.

## Contents

- [Prerequisites & Setup](#prerequisites--setup)
- [Lab 1: The Memory Stack in Action](#lab-1-the-memory-stack-in-action) — 6-layer memory hierarchy, auto-memory, learning extraction
- [Lab 2: Skills & Subagents](#lab-2-skills--subagents) — progressive disclosure, domain knowledge, subagent isolation
- [Lab 3: MCP — Browser Snapshot](#lab-3-mcp--browser-snapshot) — extending agent capabilities with MCP servers
- [Lab 4: Hooks That Bite](#lab-4-hooks-that-bite) — tiered safety hooks, auto-linting, PreToolUse/PostToolUse
- [Lab 5: The Ralph Wiggum Loop](#lab-5-the-ralph-wiggum-loop) — bounded autonomy with goal, promise, and limit
- [Lab 6: The Overnight Harness](#lab-6-the-overnight-harness) — multi-context-window autonomy, state files, shell loop
- [Lab 7: Subagents in Parallel](#lab-7-subagents-in-parallel) — parallel investigations, context isolation
- [Troubleshooting](#troubleshooting)
- [Quick Reference: Files Used Per Lab](#quick-reference-files-used-per-lab)

---

## Prerequisites & Setup

Complete these steps **before the workshop**. Budget 15-20 minutes.

### 1. Required Software

Install each tool if the check command fails. Pick the command for your OS.

**Node.js v20+** — check: `node --version`

| OS | Install command |
|----|----------------|
| macOS | `brew install node@20` |
| Ubuntu/Debian | `curl -fsSL https://deb.nodesource.com/setup_20.x \| sudo -E bash - && sudo apt-get install -y nodejs` |
| Windows | Download installer from [nodejs.org](https://nodejs.org/) |

**Git** — check: `git --version`

| OS | Install command |
|----|----------------|
| macOS | `brew install git` |
| Ubuntu/Debian | `sudo apt-get install git` |
| Windows | Download from [git-scm.com](https://git-scm.com/) (includes Git Bash) |

**Claude Code** — check: `claude --version`

```bash
npm install -g @anthropic-ai/claude-code
```

**jq** (needed for Lab 4) — check: `jq --version`

| OS | Install command |
|----|----------------|
| macOS | `brew install jq` |
| Ubuntu/Debian | `sudo apt-get install jq` |
| Windows | `choco install jq` (admin PowerShell) or download from [jqlang.github.io](https://jqlang.github.io/jq/download/) |

**GitHub CLI** (needed for Lab 2) — check: `gh --version`

| OS | Install command |
|----|----------------|
| macOS | `brew install gh` |
| Ubuntu/Debian | See [cli.github.com/manual/installation](https://cli.github.com/manual/installation) |
| Windows | `choco install gh` or download from [cli.github.com](https://cli.github.com/) |

### 2. Install Browser Snapshot MCP Server (for Lab 3)

Open-source MCP server from Apra Labs — gives Claude browser screenshot capabilities. Uses Puppeteer (Chromium downloads automatically during install).

```bash
git clone https://github.com/Apra-Labs/browser-snapshot-agent.git
cd browser-snapshot-agent
npm install            # downloads Chromium (~200MB) — may take a few minutes
                       # Windows: if install fails, try: npm install --legacy-peer-deps
```

Note the **full path to `mcp-server.js`** — you'll need it in Lab 3:
```bash
# macOS/Linux:
echo "$(pwd)/mcp-server.js"
# Windows (Git Bash):
echo "$(pwd -W)\\mcp-server.js"
```

### 3. Git Authentication (for pushing from labs)

Labs 4–7 commit and push code to your fork. Git needs authentication to push. The recommended approach uses **short-lived tokens managed by the GitHub CLI** — no long-lived tokens stored on disk.

**If you already authenticated `gh` (next section), git push will just work.** The GitHub CLI configures git to use its credential helper automatically.

**Verify git can push** (after completing step 4 below):
```bash
cd agentic-workshop-demo
git push --dry-run
```

If this fails with an authentication error, run:
```bash
gh auth setup-git
```

This configures git to use `gh` as its credential helper — tokens are short-lived and managed by `gh`, not stored in plaintext. **Do not** paste personal access tokens into terminal prompts, environment variables, or config files during the workshop — that's the unsafe pattern we're teaching people to avoid.

### 4. Fork & Clone the Demo Project

Several labs (especially Lab 2: Skills and Lab 6: Overnight Harness) need a **real GitHub repo with Actions enabled**. Fork the NoteAPI demo so you have your own copy:

**Step A: Fork on GitHub**

1. Go to [github.com/Apra-Labs/agentic-workshop-demo](https://github.com/Apra-Labs/agentic-workshop-demo)
2. Click **Fork** (top right)
3. Keep the default settings and click **Create fork**

**Step B: Enable GitHub Actions on your fork**

GitHub disables Actions on forks by default. Enable them:

1. In your forked repo, go to **Settings > Actions > General**
2. Under "Actions permissions", select **Allow all actions and reusable workflows**
3. Click **Save**
4. Go to the **Actions** tab and click **I understand my workflows, go ahead and enable them**

**Step C: Clone your fork**

```bash
# Replace YOUR_USERNAME with your GitHub username
git clone https://github.com/YOUR_USERNAME/agentic-workshop-demo.git
cd agentic-workshop-demo
```

**Step D: Trigger a CI run** (so Lab 2 has data to query)

```bash
# Push any trivial change to trigger the workflow
git checkout -b feature/workshop-setup
git commit --allow-empty -m "chore: trigger CI for workshop"
git push -u origin feature/workshop-setup
```

Go to your fork's **Actions** tab — you should see a CI run in progress or completed.

**Step E: Also clone the workshop repo** (for reference)

```bash
cd ..
# The workshop repo URL will be shared by the instructor
git clone --recurse-submodules https://github.com/Apra-Labs/agentic-ai-workshop
```

### 5. Install Dependencies

In your forked `agentic-workshop-demo` directory:

```bash
cd agentic-workshop-demo
npm install
```

**Verify everything works:**
```bash
npm test
```

Expected output: **21 tests passing**, 2 test suites, 0 failures.

```bash
npm run lint
```

Expected: clean exit, no errors.

**Authenticate GitHub CLI** (needed for Lab 2):
```bash
gh auth login
```
Follow the prompts to authenticate with your GitHub account.

### 6. Enable Auto-Memory (for Lab 1)

Auto-memory is a gradual rollout feature. Without this step, Lab 1's auto-memory demo won't work — Claude will acknowledge your "remember" requests verbally but won't persist them to disk.

**macOS / Linux** — add to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.):
```bash
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=0
```
Then restart your terminal or `source` the profile.

**Windows** — this is already included in step 7's `.bashrc` block. If you complete step 7, you're covered. If you skipped step 7 (e.g., using WSL), add the same `export` line to your shell profile.

### 7. Windows Git Bash Setup (Windows only)

Labs use hooks and shell scripts that require bash. On Windows, **Git Bash is the recommended terminal**. Run this once to fix PATH, `jq`, auto-memory, and interactive mode:

```bash
cat >> ~/.bashrc << 'EOF'
export PATH="$PATH:$(cygpath "$APPDATA/npm")"
export PATH="$PATH:/c/ProgramData/chocolatey/bin"
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=0
alias claude="winpty claude.cmd"
EOF
source ~/.bashrc
```

- The `CLAUDE_CODE_DISABLE_AUTO_MEMORY=0` line enables auto-memory (Lab 1). Without it, Claude won't persist notes across sessions.
- The alias is needed because Git Bash's mintty terminal hangs on interactive programs — `winpty` bridges it. Not needed for `claude -p` (Lab 6 harness).

**Verify:**
```bash
claude --version && jq --version && echo '{}' | jq '.'
```

### 8. VS Code Workspace Setup (Recommended)

You'll spend the entire workshop in one VS Code window — project files on top, Claude in the terminal below, lab instructions on the side. No need to leave VS Code.

**Step A: Import the workshop VS Code profile**

The workshop includes a pre-configured VS Code profile with a light theme, large fonts, and Git Bash as the default terminal — optimized for screen sharing and readability.

1. Open VS Code
2. **Ctrl+Shift+P** → type **"Profiles: Import Profile"** → select it
3. Choose **"Select File..."** and open `advanced/vscode-workshop-profile.json` from the workshop repo
4. Name the profile **"Workshop"** and click **Create**
5. VS Code switches to the new profile automatically

> **To switch back after the workshop:** Click the gear icon (bottom-left) → **Profiles** → select your original profile (e.g., "Default").

**Step B: Open the demo project in VS Code**

```bash
code agentic-workshop-demo
```

Or: File > Open Folder > select your `agentic-workshop-demo` directory.

**Step C: Verify Git Bash is your default terminal**

If you imported the workshop profile (Step A), Git Bash is already set. Open a new terminal with **Ctrl+`** — the prompt should show `$`, not `PS>` or `C:\>`. If not, click the dropdown arrow next to **+** in the terminal panel → **Select Default Profile** → **Git Bash**.

**Step D: Open lab materials for reference**

The instructor will share `lab-materials.md` with you. Open it in VS Code:

1. File > Open File > select `lab-materials.md`
2. Right-click the tab > **Open Preview** (or **Ctrl+Shift+V**) for rendered markdown
3. Drag the preview tab to the right side of the editor to create a split view

Your layout should now be:
```
┌─────────────────────────┬──────────────────────┐
│  Project files (editor) │  Lab Materials (md)  │
│                         │                      │
├─────────────────────────┴──────────────────────┤
│  Git Bash terminal (Claude runs here)          │
└────────────────────────────────────────────────┘
```

**Step E: Verify the setup**

In the VS Code terminal:
```bash
claude --version && echo "Ready for labs"
```

### 9. Verify Claude Code

In the VS Code terminal, start Claude Code:
```bash
claude
```

You should see Claude start up and load the project. Type `/exit` to exit. If you see CLAUDE.md being loaded in the startup output, you're good.

---

## Lab 1: The Memory Stack in Action

**Time:** ~5 minutes
**What you'll learn:** The 6-layer memory hierarchy — system prompt, CLAUDE.md, path-scoped rules, user memory, local memory, and auto-memory — plus the learning extraction habit.

### The Problem
Without persistent memory, every new Claude session rediscovers your project from scratch — the test database port, the preferred package manager, the API naming conventions. You've probably hit the same issue twice in different sessions. The memory hierarchy fixes this by giving the agent six layers of persistent knowledge, each with a different scope and purpose. The layers you control: CLAUDE.md (team, committed to git), path-scoped rules (file-pattern-specific), user memory (personal, all projects), local memory (personal + this project, gitignored), and auto-memory (Claude's own notes).

### Step 1: Explore CLAUDE.md

Open `CLAUDE.md` in your editor.

**What to observe:**
- It's short — build commands, code conventions, and "what NOT to do"
- No formatting rules (that's the linter's job)
- Every line exists because someone hit a problem without it

### Step 2: Explore Path-Scoped Rules

Open `.claude/rules/api-conventions.md` in your editor.

**What to observe:**
- The YAML frontmatter at the top:
  ```yaml
  ---
  paths:
    - "src/api/**/*.ts"
  ---
  ```
- These rules **only activate** when Claude touches files matching that glob pattern
- The rules are specific: input validation, error wrapping, HTTP status codes
- Files outside `src/api/` never see these rules — zero noise

### Step 3: Auto-Memory (Live)

Start Claude Code in your forked `agentic-workshop-demo` directory:
```bash
cd agentic-workshop-demo
claude
```

Type this prompt:
```
Remember that I prefer using --verbose flag when running tests locally
```

**What to observe:**
- Claude saves a note to its auto-memory directory
- The memory file lives at: `~/.claude/projects/<project-hash>/memory/MEMORY.md`
- Next session — even after a context overflow — Claude already knows this

**To verify:** Press **Ctrl+O** to expand Claude's thinking. You should see a line like:
```
Wrote 1 lines to ..\..\Users\<User>\.claude\projects\C--akhil-agentic-workshop-demo\memory\MEMORY.md
```

**Alt+click** on that path to open the memory file directly and see what Claude wrote.

> **Not seeing the write?** Make sure `CLAUDE_CODE_DISABLE_AUTO_MEMORY=0` is set in your environment (setup step 6/7). Without it, Claude acknowledges "remember" requests verbally but doesn't persist them.

Now try a second prompt:
```
Remember that the test database runs on port 5433, not the default 5432.
```

Press **Ctrl+O** again — you should see another write to the same `MEMORY.md`. Open it: Claude appended the new fact alongside the first one. This file persists across sessions and context overflows.

### Step 4: Learning Extraction (Live)

Still in Claude Code, paste this prompt:
```
We just spent time figuring out that the test DB uses port 5433. What addition to CLAUDE.local.md would have prevented this on the first try?
```

**What to observe:**
- Claude suggests a specific addition to CLAUDE.local.md (personal, gitignored — because a test DB port is a local preference, not a team convention)
- This is the **learning extraction habit** — every struggle becomes a future shortcut
- Over time, your project instructions compound: session 1 you struggle with 10 things, session 20 you struggle with 1

### Key Takeaways
- **CLAUDE.md compounds over time** — session 1 you struggle with 10 things, session 20 you struggle with 1. Every line you add prevents a future debugging session.
- **Path-scoped rules reduce noise** — in a large codebase, loading all rules into every context wastes the agent's attention budget. Scope rules to the files they apply to.
- **Auto-memory is Claude Code only** — it won't carry over if you also use Cursor or Copilot. CLAUDE.md (committed to git) is the portable layer.
- **CLAUDE.md is advisory, not enforced** — the agent follows it most of the time, but it's not guaranteed. Hooks (Lab 4) add automation and catch common mistakes, but for hard enforcement, you need a sandbox or architecture controls.
- **Don't put formatting rules in CLAUDE.md** — that's the linter's job. Never send an LLM to do a linter's job.

### Try It Yourself
- Ask Claude to remember something specific to your own projects
- Open your own project's CLAUDE.md and identify one thing that cost you debugging time but isn't documented yet

### Homework: Study a Real-World Memory Stack

Browse the CLAUDE.md and `.claude/` directory of the ApraPipes project — a real C++ video processing framework:

**[github.com/Apra-Labs/ApraPipes/CLAUDE.md](https://github.com/Apra-Labs/ApraPipes/blob/main/CLAUDE.md)**

Also browse the `.claude/` directory — it has `LEARNINGS.md` (accumulated debugging knowledge), `CURRENT_STATE.md` (what the agent is working on), `commands/`, `scripts/`, `settings.json`, and `skills/`. Compare with the workshop demo's minimal setup — notice how a production project accumulates more layers over time.

### Thinking Exercise

1. **Layer selection:** A developer discovers that `npm test -- --verbose` shows better output. Should this go in CLAUDE.md (team, committed), CLAUDE.local.md (personal, gitignored), auto-memory (agent-managed), or a path-scoped rule? What's your reasoning? Would your answer change if the whole team preferred verbose output?

2. **CLAUDE.md growth:** Over 6 months, your CLAUDE.md grows from 20 lines to 300 lines. At what point does a long CLAUDE.md start hurting the agent instead of helping? How would you decide what to prune, what to move to path-scoped rules, and what to keep?

3. **Auto-memory trust:** Auto-memory is written by the agent, not the human. What happens if the agent writes something wrong — say, it memorizes a workaround that only worked because of a temporary bug? Who reviews auto-memory, and how often?

4. **Cross-tool portability:** CLAUDE.md is Claude Code's convention. Cursor uses `.cursorrules`. Copilot uses `.github/copilot-instructions.md`. If your team uses multiple tools, how do you avoid maintaining three copies of the same instructions? Is there a single-source-of-truth approach?

5. **The "don't" list:** The demo's CLAUDE.md has a "What NOT To Do" section. Why is telling the agent what *not* to do sometimes more effective than telling it what *to* do? When does a "don't" list become counterproductive?

---

## Lab 2: Skills & Subagents

**Time:** ~5 minutes
**What you'll learn:** How skills provide domain knowledge via progressive disclosure, and how subagents keep your main context clean.

### The Problem
Every time you ask the agent to do something domain-specific — watch a CI build, debug a pipeline, parse logs — it rediscovers the domain from scratch. This eats context, slows you down, and the knowledge doesn't persist between sessions. Skills solve this by packaging domain knowledge into markdown files that load on demand. And when you pair skills with subagents, the investigation happens in a separate context — your main session stays clean.

### Step 1: Explore the Skill File

Open `.claude/skills/noteapi-devops/SKILL.md` in your editor.

**What to observe:**
- The file has two sections:
  - **Name + description** (top) — loaded at startup, always visible to Claude
  - **Full body** (everything else) — loaded only when the skill is invoked
- This is **progressive disclosure** — skills don't bloat your context until you need them
- The skill contains: CI pipeline structure, `gh` commands, failure interpretation logic, reporting format

### Step 2: Discover Available Skills (Live)

Start Claude Code in your forked `agentic-workshop-demo` directory:
```bash
cd agentic-workshop-demo
claude
```

Type this prompt:
```
What custom skills do you have in this project?
```

**What to observe:**
- Claude lists the `noteapi-devops` skill — it loaded the name + description at startup
- It knows what the skill does without loading the full body yet — that's progressive disclosure in action
- If you had multiple skills in `.claude/skills/`, they'd all appear here

### Step 3: Invoke the Skill via Subagent (Live)

Before running the skill, check your current context usage:
```
/context
```

Note the numbers — you'll compare after the subagent returns.

Now type this prompt:
```
Spin up a subagent with the noteapi-devops skill to check if the last CI pipeline passed and summarize any failures.
```

**What to observe:**
- Claude spawns a **subagent** — a separate context window running in the background
- Your main context stays clean (the DevOps investigation happens in the subagent's context)
- The result comes back as a condensed summary (1-2K tokens), not the full 20K investigation
- Run `/context` again — your main context grew by only the summary size, not the full investigation
- Because you forked the repo and triggered a CI run during setup, the skill has real data to query

**If you see "no workflow runs found":** Make sure you completed step 4D in the setup (push an empty commit to trigger CI) and that GitHub Actions is enabled on your fork.

### Key Takeaways
- **If you've had the same discovery conversation 3+ times, extract it into a skill** — the skill persists, the conversation doesn't.
- **Progressive disclosure saves context** — only the name + description load at startup (~100 tokens); the full body loads only when invoked. No bloat until you need it.
- **Subagents keep investigations isolated** — the DevOps investigation happened in its own context. Your main session didn't pay for it.
- **Skills are an open standard** — the AAIF spec (agentskills.io) means the same skill file works across Claude Code, GitHub Copilot, and other tools that implement it. Write once, use everywhere.
- **Review third-party skills before installing** — a skill can include shell commands. Treat them like any dependency.

### Try It Yourself
- Try invoking the skill after pushing a change that breaks linting or tests — see how the skill reports the failure
- Create a simple skill for something you rediscover every session (e.g., your project's deploy process, a complex API you use frequently)

### Homework: Explore a Production-Grade Skill

The `noteapi-devops` skill you just used is intentionally simple — one file, one workflow. Real skills can be much more sophisticated. Browse this production skill used at Apra Labs for multi-platform CI/CD troubleshooting:

**[github.com/Apra-Labs/ApraPipes/.claude/skills/aprapipes-devops](https://github.com/Apra-Labs/ApraPipes/tree/main/.claude/skills/aprapipes-devops)**

```
.claude/skills/aprapipes-devops/
├── SKILL.md                         ← Entry point: routing logic + quick reference
├── methodology.md                   ← 4-phase debugging process
├── reference.md                     ← Cross-platform operational reference (38 KB)
├── troubleshooting.containers.md    ← Docker-specific issues
├── troubleshooting.cuda.md          ← GPU/CUDA issues
├── troubleshooting.jetson.md        ← ARM64 Jetson edge devices
├── troubleshooting.linux.md         ← Linux x64 CI issues
├── troubleshooting.macos.md         ← macOS CI issues
├── troubleshooting.vcpkg.md         ← Package management issues
├── troubleshooting.windows.md       ← Windows CI issues
└── scripts/
    ├── debug-cudnn-local.sh         ← Docker-based cuDNN debugging
    └── test-gh-cache-delete.sh      ← Cache deletion testing
```

**What to notice:**
- **SKILL.md is a router, not a manual** — it contains a decision tree that maps failure types to the correct troubleshooting file. The agent loads SKILL.md first, then pulls in only the relevant sub-file. This is progressive disclosure taken further: not just "load on invoke" but "load only the slice you need."
- **The skill encodes operational wisdom, not just commands** — `methodology.md` teaches a 4-phase debugging process (Detect → Validate Locally → Test in Cloud → Verify & Roll Out). The critical first step: "ALWAYS compare working vs broken with `git diff` before doing anything else." This isn't tool documentation — it's how a senior engineer thinks.
- **The skill grows with every fix** — the methodology includes an explicit feedback loop: after every fix, update the error pattern lookup table in SKILL.md and add a detailed entry to the relevant troubleshooting file. The skill gets smarter over time.
- **Platform-specific files prevent cross-contamination** — Jetson ARM64 issues (JetPack 5.x, gcc-9.4, CUDA 11.4) live in their own file, separate from Windows issues (Python distutils, disk exhaustion). Loading Jetson troubleshooting doesn't waste context on Windows problems.

### Thinking Exercise

After browsing the ApraPipes skill, consider these questions:

1. **Context budget:** `reference.md` alone is 38 KB (~10K tokens). If every troubleshooting file loaded at once, the skill would consume ~40K tokens — nearly half a typical context window. How does the routing pattern in SKILL.md prevent this? What would happen to agent performance if you put everything in a single file instead?

2. **Knowledge vs. instructions:** `methodology.md` says "NEVER push fixes blindly to GitHub Actions — test locally first." This isn't a tool command — it's a judgment call that prevents wasted CI minutes. What other judgment calls in your own domain could be encoded as skill instructions? How would you distinguish "always do this" rules from "use your judgment" guidelines?

3. **The feedback loop problem:** The skill says "after every fix, update the skill files." In practice, who does this — the human or the agent? What happens if the agent updates the skill with a workaround that turns out to be wrong? How would you review skill updates differently from code changes?

4. **Skill boundaries:** This skill covers CI/CD debugging across 5 platforms with CUDA, vcpkg, Docker, and ARM64. Would you split it into 5 platform-specific skills instead? What are the trade-offs? (Hint: think about cross-platform issues like the Python distutils removal that affected both Windows and Linux simultaneously.)

5. **Security surface:** The skill includes shell scripts (`debug-cudnn-local.sh` launches a Docker container with `--gpus all` and mounts your working directory). If you installed a third-party skill with similar scripts, what risks would you be accepting? How does this relate to the hooks and guardrails you'll explore in Lab 4?

---

## Lab 3: MCP — Browser Snapshot

**Time:** ~5 minutes
**What you'll learn:** How MCP extends Claude's capabilities beyond file I/O and shell commands.

### The Problem
Without MCP, your agent can read files, write code, and run shell commands — that's it. It can't see what a browser renders, query a database directly, or post to Slack. Every external interaction requires you to copy-paste results into the conversation. MCP (Model Context Protocol) is an open protocol that connects agents to external tools through a standardized interface — think of it as **USB-C for AI**: one protocol, any tool, any agent. In this lab, you'll give Claude the ability to capture browser screenshots — something it simply cannot do natively.

### Step 1: Configure the MCP Server

You cloned `browser-snapshot-agent` during setup (step 2). You'll need the full path to `mcp-server.js` inside that directory. If you forgot where you cloned it:
```bash
# Find it:
find ~ -name "mcp-server.js" -path "*/browser-snapshot-agent/*" 2>/dev/null

# Windows (Git Bash):
find /c/Users/$USER -name "mcp-server.js" -path "*/browser-snapshot-agent/*" 2>/dev/null
```

**Where to configure: user-level vs project-level**

Claude Code reads MCP config from two places:

| Level | File | Scope | Use when |
|-------|------|-------|----------|
| **User-level** | `~/.claude/settings.json` | Available in **every project** | Personal tools you use everywhere (browser, Slack, DB) |
| **Project-level** | `.claude/settings.json` (in repo) | This project only, shared with team | Project-specific tools (custom APIs, internal services) |

Browser screenshots are useful everywhere, so configure at **user-level** — one setup, available in every project you open:

| Platform | Settings file |
|----------|--------------|
| macOS / Linux | `~/.claude/settings.json` |
| Windows | `%USERPROFILE%\.claude\settings.json` |

Add or merge this (use the path you noted during setup step 2):

**macOS / Linux:**
```json
{
  "mcpServers": {
    "browser-snapshot": {
      "command": "node",
      "args": ["/full/path/to/browser-snapshot-agent/mcp-server.js"]
    }
  }
}
```

**Windows:**
```json
{
  "mcpServers": {
    "browser-snapshot": {
      "command": "node",
      "args": ["C:\\full\\path\\to\\browser-snapshot-agent\\mcp-server.js"]
    }
  }
}
```

Replace the path with the actual output from the `echo` command in setup step 2.

> **Why user-level?** If you put this in a project's `.claude/settings.json`, it only works in that project and the path must be valid for every team member. User-level config is personal — your paths, your tools, available everywhere.

### Step 2: Verify MCP Tools Are Available

Start Claude Code in any directory:
```bash
claude
```

Type the built-in command:
```
/mcp
```

**What to observe:**
- You'll see the `browser-snapshot` server listed with its connection status
- It shows all registered tools: `capture_snapshot`, `capture_multiple_viewports`, `capture_responsive_set`, `capture_from_config`
- This is how you diagnose MCP issues — if a server fails to connect, `/mcp` shows the error

> **`/mcp` is your MCP dashboard** — use it anytime you add a new MCP server, change config, or suspect a connection issue. It shows every server, its status, and the tools it provides.

### Step 3: Capture Two Screenshots (Live)

Type this prompt:
```
Capture a mobile screenshot of https://blub0x.com and suggest 3 important improvements
```

**What to observe:**
- Claude calls `capture_snapshot` — a tool it didn't have before MCP
- A mobile screenshot appears in your project directory
- Claude then **reads the screenshot** and gives UI/UX feedback — it's using the image as context, not just capturing it
- One prompt triggered two capabilities: browser access (MCP) + visual analysis (multimodal)

### What to Notice:
- Claude didn't need a browser extension, a Selenium setup, or custom code
- MCP gives the agent access to a browser screenshot tool the same way it accesses `Read` or `Bash` — just another tool
- The MCP server runs as a **local process with your user's permissions** — treat it like any npm package (audit before installing)

### Key Takeaways
- **MCP is a protocol, not a product** — the same MCP server works with Claude Code, GitHub Copilot, Cursor, and any tool that speaks MCP. You configure it once.
- **MCP servers run as local processes with your user's permissions** — they are not sandboxed by default. A poorly written MCP server has the same access as any program you run locally. Audit before installing, like any npm package.
- **Not everything needs MCP** — if the agent can already do it with Read/Write/Bash (file operations, shell commands), MCP adds complexity without benefit. MCP is for capabilities the agent doesn't have natively: browser access, database queries, Slack/Jira integration.
- **The tool list appeared automatically** — once configured, MCP tools show up alongside built-in tools. No special syntax needed. The agent calls `capture_snapshot` the same way it calls `Read` or `Bash`.

### Homework
- **Full responsive set:** Ask Claude to `capture a responsive screenshot set of https://blub0x.com` — this captures 7 viewports (desktop, tablet, mobile) in one call
- **Style analysis:** After capturing, ask Claude to `analyze these snapshots and suggest style improvements` — Claude reads the images and gives UI/UX feedback
- **Your own site:** Try both on your own project's URL or local dev server

---

## Lab 4: Hooks That Bite

**Time:** ~5 minutes
**What you'll learn:** How hooks automate quality checks and catch accidental mistakes — with a tiered approach (hard block vs. ask the user) — and why they're *not* a security boundary.

### The Problem
In the basic workshop you learned about the autonomy spectrum — from Nanny mode (approve everything) to YOLO mode (approve nothing). Most teams land somewhere in between with an **allowlist**: permit specific safe commands, block everything else. That works, but it's friction-heavy — you're constantly approving routine operations.

Hooks come in two flavors, with very different purposes:
- **PreToolUse hooks** catch common accidents — a sloppy `rm -rf`, an unintended `git push --force`. They're a safety net for mistakes, not a security boundary. An agent that wants to delete files can use `find . -delete` or a Python script — no regex will catch every alternative. For real security, you need the sandbox (Level 2) or architecture (Level 1). But PreToolUse hooks have a powerful trick: they can respond in **three ways** — hard block (exit 2), ask the user for confirmation (exit 0 + JSON), or allow silently (exit 0).
- **PostToolUse hooks** automate quality — auto-linting after every edit, auto-running tests after changes. This is where hooks genuinely shine. They turn "remember to lint" into "linting is automatic."

### Before You Start: Create a Lab Branch

From this lab onward, each lab gets its own git branch — `main` stays pristine as your reset point. And Claude handles the git plumbing.

Start Claude Code in your forked `agentic-workshop-demo` directory with **full permissions**:
```bash
cd agentic-workshop-demo
claude --dangerously-skip-permissions
```

Notice: no permission prompts. The agent can do anything — read, write, execute, delete. Hooks (which you're about to explore) catch common mistakes and automate quality checks — but for real security, you'd need a sandbox or architecture controls.

Type:
```
Create a branch called lab4 from main and switch to it.
```

That just worked — no approval prompt. One sentence, no git syntax to remember. This is the speed you get with full autonomy.

### Step 1: Explore the Hooks Configuration

Open `.claude/settings.json` in your editor.

**What to observe:**
- **`permissions.allow`** at the top — this is the **allowlist** you've been benefiting from in Labs 1-3. Commands like `gh`, `git`, `npm test` were pre-approved here so you didn't get prompted for every routine operation. This is the allowlist approach: block by default, permit what's known-safe.
- **PreToolUse hook** on `Bash` matcher — runs `block-destructive.sh` before every shell command
- **PostToolUse hook** on `Write|Edit` matcher — auto-runs the linter after every file edit
- Hooks are configured per-project (in `.claude/settings.json`) or globally (in `~/.claude/settings.json`)
- Notice the two approaches side by side: **allowlist** (permissions) pre-approves specific commands. **Hooks** add automation (post-edit linting) and catch common mistakes (accidental `rm -r`). Neither is a security boundary — for that, you need the sandbox or architecture layers from the slide.

### Step 2: Read the Hook Script

Open `.claude/hooks/block-destructive.sh`:

```bash
#!/bin/bash
# PreToolUse hook: tiered response to risky commands
# Exit 2 = hard block (irreversible, high blast radius)
# Exit 0 + JSON "ask" = prompt user for confirmation (moderate risk)
# Exit 0 (no JSON) = allow silently

INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

# Tier 1: HARD BLOCK — irreversible, high blast radius
if echo "$COMMAND" | grep -qE 'git\s+push\s+--force|drop\s+table|truncate\s+table'; then
  echo "BLOCKED: Irreversible command detected: $COMMAND"
  exit 2
fi

# Tier 2: ASK USER — destructive but sometimes intentional
if echo "$COMMAND" | grep -qE 'rm\s+-r'; then
  cat <<HOOK_JSON
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "ask",
    "permissionDecisionReason": "Recursive delete detected: $COMMAND"
  }
}
HOOK_JSON
  exit 0
fi

# Everything else: allow silently
exit 0
```

**What to observe — the tiered approach:**
- The hook reads JSON from stdin (Claude passes tool call details), extracts the command, and checks it against regex patterns.
- **Tier 1 — Hard block (exit 2):** `git push --force`, `DROP TABLE`, `TRUNCATE TABLE`. The agent **cannot** proceed past exit code 2 — this is deterministic, not advisory. The stdout message is fed back to the agent explaining why.
- **Tier 2 — Ask the user (exit 0 + JSON):** `rm -r` (recursive delete). Instead of hard-blocking, the hook returns a `permissionDecision: "ask"` JSON response. This triggers a confirmation prompt — the user can approve or deny. This handles the false-positive problem: `rm -r ./dist` (routine cleanup) gets approved; `rm -r /` (catastrophe) gets denied.
- **Tier 3 — Allow silently (exit 0, no JSON):** Everything else passes through with no prompt and no delay.

**Prerequisite check:** This script requires `jq`. Verify it's installed:
```bash
jq --version
```
If not installed, see the setup instructions above.

### Step 3: Trigger a Hard Block (Live)

You're running with `--dangerously-skip-permissions` — the agent has full autonomy. Let's see if the hook still holds.

Type this prompt:
```
Force push the current branch to origin main using git push --force origin main
```

**What to observe:**
- Claude attempts to run the command
- The PreToolUse hook intercepts it **before execution**
- You see: `BLOCKED: Irreversible command detected: git push --force origin main`
- Claude receives this feedback and explains that the command was blocked
- **The command never ran.** Even in full YOLO mode, exit code 2 is an absolute stop. The agent cannot override it.

### Step 4: Trigger a Confirmation Prompt (Live)

Now let's see the second tier — the "ask" response. Type:
```
Clean up dependencies by running rm -rf ./node_modules
```

**What to observe:**
- Claude attempts to run `rm -rf ./node_modules`
- The PreToolUse hook catches the `rm -r` pattern — but instead of hard-blocking, it returns `permissionDecision: "ask"`
- **You see a confirmation prompt** asking whether to allow or deny the recursive delete
- You can approve (it's just node_modules, `npm install` restores it) or deny
- This is the difference: `git push --force` is always wrong (hard block), but `rm -r` is sometimes intentional (ask the user)

> **Note:** If you're running with `--dangerously-skip-permissions`, the "ask" prompt may be auto-approved (the YOLO flag auto-approves all permission dialogs). To see the confirmation dialog in action, exit Claude Code and restart without the flag: just `claude`. Then try the `rm -rf` prompt again. This teaches an important lesson: **exit 2 (hard block) works in ALL modes. The "ask" pattern works when the user is in the loop.**

### Step 5: See PostToolUse Auto-Linting (Live)

The PostToolUse hook runs `npm run lint:fix` after every file Write or Edit. Ask Claude:
```
Add a console.log("hello") to src/api/notes.ts at the top of the GET handler.
```

**What to observe:**
- Claude edits the file
- Immediately after the edit, the PostToolUse hook fires and runs the linter automatically
- The linter catches the `console.log` violation (CLAUDE.md says "No console.log in route handlers") and either fixes or flags it
- **You didn't have to ask Claude to lint. You didn't have to remember. The hook did it for you — every single time, on every file edit.**

This is the productivity win: hooks turn "remember to lint" into "linting is automatic." Over a full day of coding, that's dozens of manual steps eliminated.

### Wrap Up

In Claude Code:
```
Commit and push any changes on this branch with a descriptive message, then switch back to main.
```

Your Lab 4 work is preserved on the `lab4` branch. `main` is clean for the next lab. Notice: you didn't type a single git command — branching, committing, and switching all happened via natural language.

### Key Takeaways
- **Tiered response is smarter than binary block/allow** — hard block (exit 2) for irreversible commands, ask the user (exit 0 + `permissionDecision: "ask"`) for risky-but-sometimes-intentional commands, allow silently for everything else. This eliminates most false positives.
- **Exit 2 works in ALL modes** — even with `--dangerously-skip-permissions`, the hard block fires. The "ask" pattern only prompts when the user is in the loop (not in YOLO mode). Choose your tier based on blast radius.
- **PreToolUse hooks catch accidents, not adversaries** — they're a speed bump for common mistakes, not a security wall. The agent can read the hook script and work around the regex. For security, use the sandbox or architecture layers.
- **PostToolUse hooks are the real productivity win** — auto-linting after every edit, auto-running tests, auto-formatting. These compound over a full day of coding — dozens of manual "remember to..." steps eliminated.
- **PreToolUse hooks have better uses than blocking commands** — logging all commands to an audit trail, validating that file edits stay within allowed directories, injecting environment variables before execution, or adding cost tracking metadata. These add value without pretending to be a security boundary.
- **Windows caveat:** Hook scripts use bash syntax. Run Claude Code from Git Bash or WSL, not PowerShell or cmd directly.

### Try It Yourself
- Add a third tier to the hook: commands matching `docker rm` should hard-block (exit 2), but `docker stop` should "ask"
- Try writing a path-aware hook: hard-block `rm -r` on `~`, `.git`, or `/` paths, but "ask" for everything else
- Try writing a PostToolUse hook that runs tests after every edit to `src/` files
- Try writing a hook that blocks commands containing sensitive paths like `~/.ssh` or `~/.aws`
- Browse the full list of hook lifecycle events (14 total) in the reference: **[claude.ai/docs/hooks](https://docs.anthropic.com/en/docs/claude-code/hooks)** — PreToolUse and PostToolUse are just two of them. Others include `Notification`, `Stop`, `SubagentStop`, and more.

### Thinking Exercise

1. **Hooks without YOLO mode:** Imagine you didn't use `--dangerously-skip-permissions` and didn't have `permissions.allow`. Every command gets a permission prompt. What value does the PreToolUse hook still provide? (Hint: it's click #247 — the one you approve on autopilot.) What about the PostToolUse auto-lint hook — does it care about permission mode at all?

2. **"Should I always use `--dangerously-skip-permissions`?"** Someone on your team will ask this. The honest answer depends on your safety architecture. Run through this checklist before answering:
   - Are secrets in CI/CD (not `.env` files on disk)?
   - Is branch protection enabled (agent can't push to main)?
   - Do hooks block destructive patterns?
   - Are credentials absent from the agent's machine?
   - Is the worst case a messy feature branch, not a production breach?
   If all five are true, `--dangerously-skip-permissions` is reasonable — there's nothing dangerous left to skip. If any are missing, fix the gap first. The flag isn't the risk — the missing architecture is.

3. **Adversarial bypass (this one is real):** During testing, an earlier version of this hook only blocked `rm -rf`. The agent recognized the hook, and used `rm -r` (no `-f` flag) instead — same result, hook bypassed. We fixed the regex to `rm\s+-r`, but `find . -delete` or `shutil.rmtree()` in a Python script would still work. How would you design hooks that block *intent* rather than *syntax*? Is that even possible?

4. **False positives solved by tiering:** You just saw the solution — `rm -r` triggers an "ask" prompt instead of a hard block, so `rm -r ./dist` (routine cleanup) can be approved while `rm -r /` (catastrophe) gets denied. But what about YOLO mode, where "ask" is auto-approved? Design a hook that checks the *path* being deleted — hard block `rm -r` on sensitive directories (`/`, `~`, `.git`), ask for everything else. What regex or path logic would you use?

5. **Hook as policy:** Your team decides "all database migrations must be reviewed by a human." Could you implement this as a hook? What would the matcher and script look like? What are the limits of enforcing team policy through hooks vs. through CI/CD gates?

---

## Lab 5: The Ralph Wiggum Loop

**Time:** ~5 minutes
**What you'll learn:** The official Ralph Wiggum plugin — bounded autonomy with a clear goal, a completion promise, and a safety limit.

> **Windows users: Use Git Bash for this lab.** The Ralph Loop plugin's stop hook uses a bash script internally. On PowerShell/cmd, the hook fails due to an open Anthropic bug with Windows path resolution. Make sure you completed setup step 7 (Git Bash setup) before proceeding.

### The Problem
You have a bounded task — implement 4 TODOs in a file. You could do them one at a time, manually prompting the agent after each. But that means you're the bottleneck — you have to watch, wait, and re-prompt. What if you could give the agent a clear goal, a signal for "I'm done," and a safety limit — then walk away?

The Ralph Wiggum Loop (named after the Simpsons character who enthusiastically declares "I'm helping!" while doing questionable things) adds three constraints that turn enthusiastic flailing into bounded autonomy. Without them, the agent either stops after one step or runs forever burning your API budget.

**Five elements make it work:**

| Element | What It Does | Without It |
|---------|-------------|------------|
| **Clear goal** | "Implement all TODOs in notes.ts" | Agent wanders, does random things |
| **Binary success criteria** | "all tests pass" | Agent declares "done" when it's not |
| **State externalization** | "Update progress.md after each change" | Progress lost on context overflow |
| **Completion promise** | `<promise>TODOS_DONE</promise>` | Can't tell if it finished or crashed |
| **Max iterations** | `--max-iterations 20` | Burns your API budget on an unsolvable task |

### Step 1: Install the Plugin

The Ralph Wiggum plugin is an official Anthropic plugin for Claude Code. Install it once:

```bash
claude plugin install ralph-loop@claude-plugins-official
```

This adds three commands to your Claude Code session: `/ralph-loop:ralph-loop`, `/ralph-loop:cancel-ralph`, and `/ralph-loop:help`.

**Explore the plugin:** Type `/ralph-loop:help` inside Claude Code to see the full usage, options, and how the Stop hook mechanism works under the hood.

**Want to browse other plugins?** Type `/plugins` to search and explore the plugin registry. You'll see official and community plugins — each bundles skills, hooks, or MCP servers into a single installable package.

### Step 2: See the TODOs

Open `src/api/notes.ts` in your editor and look at lines 8-11:

```typescript
// TODO: Add pagination to GET /api/notes (query params: page, limit)
// TODO: Add input length validation (title max 200 chars, content max 10000 chars)
// TODO: Return 400 if tags array contains duplicates
// TODO: Add updatedAt timestamp on PUT (currently not updated)
```

These are real, implementable TODOs — the target for the loop.

### Step 3: Run the Loop (Live)

Start Claude Code with full permissions — the hooks from Lab 4 are still active (catching common mistakes, auto-linting):
```bash
cd agentic-workshop-demo
claude --dangerously-skip-permissions
```

First, create a branch for this lab:
```
Create a branch called lab5 from main and switch to it.
```

Then run the loop:
```
/ralph-loop:ralph-loop "Implement all TODO items in src/api/notes.ts. After each fix, run tests. Update progress.md after each change. Output <promise>TODOS_DONE</promise> when all TODOs are resolved and tests pass." --completion-promise "TODOS_DONE" --max-iterations 20
```

**What to observe:**
- Claude works on the first TODO, runs tests, updates progress.md
- When it tries to exit, the **Stop hook** intercepts and feeds the same prompt back
- Claude sees its own previous work (modified files, git history) and picks up the next TODO
- This repeats until all TODOs are resolved and it outputs `TODOS_DONE`
- `--max-iterations 20` is the safety net — if it can't finish, it stops after 20 loops

**To cancel early:** Type `/ralph-loop:cancel-ralph` or hit Ctrl+C.

### Step 4: Check the Trail

After the loop finishes, inspect `progress.md`.

**What to observe:**
- The agent logged what it changed, in what order, and whether tests passed
- You gave it autonomy. It gave you accountability. That's the deal.

### When It Works vs. When It Doesn't

| Works | Doesn't Work |
|-------|-------------|
| Well-defined tasks with clear done criteria | Vague goals ("make it better") |
| Tests exist to validate completion | No automated validation — agent can't tell when it's done |
| State tracked in files (progress.md) | State only in conversation context |
| Task fits in one context window | Task requires multiple context resets (use Lab 6 instead) |

### Key Takeaways
- **Goal + promise + limit = bounded autonomy** — all three are required. Without the limit, a stuck agent burns tokens. Without the promise, you can't tell if it finished or crashed.
- **The Stop hook is the mechanism** — when Claude tries to exit, the hook intercepts and feeds the same prompt back. Claude sees its own previous work and continues. This is not a conversation — it's a loop.
- **`/ralph-loop:cancel-ralph` is your emergency stop** — always know how to pull the plug.
- **State externalization is critical** — progress.md means the trail survives even if you cancel mid-loop.
- **Ralph is for bounded tasks** — if the task is bigger than one context window (context fills up before it's done), use the Overnight Harness (Lab 6) instead.

### Wrap Up

In Claude Code:
```
Commit all changes, push the lab5 branch to origin, and switch back to main.
```

Your Ralph Wiggum work is preserved on `lab5`. `main` is clean for Lab 6 — no manual file resets needed. Check your fork's Actions tab to verify CI passes on the branch.

### Try It Yourself
- Try the loop on a file in your own project — write clear completion criteria

---

## Lab 6: The Overnight Harness

**Time:** ~5 minutes
**What you'll learn:** How Anthropic's initializer + coding agent pattern enables agents to work across multiple context windows unattended.

In Lab 5 you used the Ralph Wiggum plugin for bounded loops inside one session. But what about tasks that span **multiple context windows**? The overnight harness restarts Claude in a fresh context each cycle, picking up from state files.

### Before You Start: Create a Lab Branch

Start Claude Code:
```bash
cd agentic-workshop-demo
claude
```

```
Create a branch called lab6 from main and switch to it.
```

The harness script will make commits — they should land on `lab6`, not `main`.

Now use this Claude session to explore the state files in Steps 1-2, then exit before running the harness.

### Step 1: Explore plan.md and feature_list.json Side by Side

Open both files in your editor:
- `plan.md` — the human-readable plan
- `feature_list.json` — the machine-readable checklist

**What to observe:**
- Each phase in `plan.md` maps to an entry in `feature_list.json`
- `plan.md` has rationale, approach, integration tests — it's for **you**
- `feature_list.json` has `name`, `description`, and `passes: true/false` — it's for the **agent**
- Why JSON? Markdown checkboxes (`[ ]` vs `[x]`) get corrupted when agents edit them across context windows. JSON is unambiguous.

| plan.md Phase | feature_list.json Entry |
|---------------|------------------------|
| Phase 1: Tag Filtering | `{ "name": "Tag filtering endpoint", "passes": false }` |
| Phase 2: Full-Text Search | `{ "name": "Full-text search", "passes": false }` |
| Phase 3: Pagination | `{ "name": "Pagination support", "passes": false }` |
| Phase 4: Note Archiving | `{ "name": "Note archiving", "passes": false }` |

### Step 2: Explore AGENT_PROMPT.md

Open `AGENT_PROMPT.md` in your editor.

**What to observe:**
- **State Files section** at the top — tells the agent where to find plan, checklist, and progress log
- **The Loop** — a numbered sequence: read plan, find next `false` feature, implement, test, update, commit
- **The hard stop** — step 7 says: "After completing one feature, output `SESSION_DONE` and stop immediately." This is critical — without it, Claude would finish all 4 features in one session and you'd never see the restart
- **Rules** — "Exactly one feature per session" is the first rule. Skip after 3 failures. Never modify the prompt or plan.
- This is the prompt that runs inside the `while true` shell loop — every fresh context window starts here

### Step 3: Read the Harness Script, Then Run It

Exit Claude Code first (`/exit`) — the harness script starts its own Claude sessions.

Open `lab6-harness.sh` (or `lab6-harness.ps1` on Windows) in your editor:
- A `while true` loop that runs `claude -p "$(cat AGENT_PROMPT.md)"` with `--max-turns 15` and `--dangerously-skip-permissions`
- After each session, checks `feature_list.json` — exits when all features have `"passes": true`
- Also checks for a `STOP` file — create it from another terminal to stop between sessions
- Echo markers between cycles so you can see when one context window ends and the next begins

Now run it:

**macOS / Linux / Git Bash:**
```bash
bash lab6-harness.sh
```

**Windows PowerShell:**
```powershell
powershell -ExecutionPolicy Bypass -File lab6-harness.ps1
```

**To stop early:** Ctrl+C (Git Bash/macOS/Linux) or from another terminal create a stop file: `touch STOP` (bash) / `New-Item STOP` (PowerShell). The harness checks for this file between sessions and exits cleanly.

**What you'll see (let it run for 2 cycles, then stop it):**

```
========================================
  STARTING NEW CONTEXT WINDOW
========================================

  → Claude reads plan.md, feature_list.json, progress.md
  → Finds "Tag filtering endpoint" (passes: false)
  → Implements it, runs tests, all pass
  → Updates feature_list.json (passes: true)
  → Updates progress.md
  → Commits: "feat: add tag filtering tests"
  → Outputs SESSION_DONE

  SESSION EXITED — restarting in 3s...

========================================
  STARTING NEW CONTEXT WINDOW
========================================

  → Claude reads plan.md, feature_list.json, progress.md
  → Skips "Tag filtering" (already true)
  → Finds "Full-text search" (passes: false)
  → Implements it, runs tests, all pass
  → ...
```

**Stop the harness after the second cycle** (Ctrl+C in Git Bash, close terminal in PowerShell). You've seen the pattern:
1. Fresh context reads state files
2. Picks up where the last session left off
3. Completes exactly one feature
4. Commits, updates state, exits
5. Shell loop restarts with clean context

**Why `--max-turns 15`?** Safety net — if Claude ignores the exit instruction and keeps going, it gets capped at 15 tool calls. The prompt enforces "one feature then stop"; `--max-turns` is the backup.

**Why `--dangerously-skip-permissions`?** No permission prompts to block the loop. This is only safe when secrets live in CI/CD (not on the machine), branch protection is enabled, and the agent can only push to feature branches — not main.

### Step 4: Check the Trail

After stopping the loop, inspect what happened:

```bash
# What did Claude commit?
git log --oneline -5

# What features are done?
cat feature_list.json

# What did Claude write about its work?
cat progress.md
```

**What to observe:**
- `feature_list.json` has 2 features flipped to `passes: true` (one per cycle you ran)
- `progress.md` has two session entries with what was built, decisions made, tests passed
- `git log` shows clean, descriptive commits — one per feature
- **This is what you'd read in the morning after an overnight run**

### How It Differs from Lab 5 (Ralph Wiggum Loop):

| | Ralph Wiggum Loop (Lab 5) | Overnight Harness (this lab) |
|---|---|---|
| **Mechanism** | Stop hook inside one session | Shell loop restarts separate sessions |
| **Context** | Same session, sees its own work | Fresh context each cycle |
| **Duration** | Minutes to an hour | Hours to days |
| **Scope** | One bounded task | Entire feature list |
| **Survives context overflow** | No — session ends | Yes — that's the whole point |
| **Install** | `claude plugin install ralph-loop@claude-plugins-official` | `bash lab6-harness.sh` |

### What Can Go Wrong

| Failure Mode | What Happens | Recovery |
|-------------|-------------|----------|
| Agent loops on a hard feature | Burns context windows without progress; feature stays `false` | Morning review: `git log` shows repeated attempts. Rewrite the feature spec or break it smaller. |
| Agent produces garbage code | Tests fail; feature stays `false`; next context window may retry | `git revert` to last good commit. Git commits are checkpoints — that's by design. |
| API budget consumed | Claude returns billing errors; shell loop keeps retrying | Set a budget alert in your Anthropic dashboard. `--max-turns` caps iterations per session. |

**Cost reality:** A single overnight run typically costs $5-30 per feature in API usage, depending on complexity and context window resets. The C compiler case study (Anthropic blog) cost ~$20K across 2,000 sessions — far beyond a typical overnight run. Set budget alerts before running unattended.

### Key Takeaways
- **State files are the contract** — plan.md for humans, feature_list.json for the agent, progress.md for accountability. Without them, each restart is blind.
- **One feature per session is deliberate** — it ensures clean restarts and prevents context exhaustion. Without the "exactly one, then stop" rule, the agent would do everything in one session and you'd never see the restart pattern.
- **Git commits are your safety net** — if a session produces bad code, `git revert` gets you back. The harness is designed for this.
- **`--dangerously-skip-permissions` requires safety architecture first** — this flag is only safe when secrets are out of the agent's reach, branch protection is on, and CI gates are in place.
- **This pattern scales** — the same architecture works for 4 features overnight or 100 features over a week. It's the same loop.

### Wrap Up

After the harness stops (either all features done, Ctrl+C, or STOP file), it has already committed as it went. Start Claude Code:
```bash
claude
```

Review what the harness accomplished:
```
Show me the git log for this branch and summarize what was built.
```

Push and return to main:
```
Push the lab6 branch to origin, then switch back to main.
```

Check your fork's Actions tab to verify CI passes. Your overnight harness work is preserved on `lab6`.

### Want to Try Again?

In Claude Code:
```
Switch to main, delete the lab6 branch, and create a fresh lab6 branch from main.
```

### Homework
- Let the loop run through all 4 features (takes ~5-10 min, costs a few dollars in API credits)
- Verify CI passes on the pushed branch
- For your own projects: create a `plan.md` and `feature_list.json` following the same structure

### Thinking Exercise

1. **Morning review:** You wake up to 4 features implemented overnight. `git log` shows 4 clean commits, tests pass, `progress.md` has detailed notes. How do you review 4 sessions' worth of agent work efficiently? What's your "morning checklist" to decide if the code is production-ready?

2. **Feature sizing:** "Add user authentication with OAuth, JWT tokens, and role-based access" is too big for one context window. "Fix a typo in a comment" is too small to justify harness overhead. How do you size features for the harness? What heuristics would you use?

3. **Failure cascades:** Feature 2 depends on Feature 1's implementation. If the agent implemented Feature 1 with a subtle design flaw, Feature 2 builds on the wrong foundation. By morning, you have 4 features built on a bad premise. How would you detect this? Could the harness architecture prevent it?

4. **JSON vs. Markdown for state:** The harness uses JSON for the feature checklist because markdown checkboxes (`[ ]` vs `[x]`) get corrupted when agents edit them across context windows. What other state formats have you seen LLMs struggle with? What makes JSON more reliable for agent-written state?

---

## Lab 7: Subagents in Parallel

**Time:** ~5 minutes
**What you'll learn:** How to use subagents for parallel investigations without bloating your main context.

### The Problem
When you need multiple things investigated simultaneously — a security review, a test coverage check, a TODO audit — doing them sequentially in your main context means each investigation's tokens crowd out the next. By the time you finish the third investigation, your context is bloated and the agent has forgotten details from the first. Subagents solve this by giving each investigation its own context window. Only the condensed summaries come back to the parent — not the full investigation.

### Before You Start: Create a Lab Branch

Start Claude Code with full permissions — by now you've seen hooks protect you in Labs 4-6:
```bash
cd agentic-workshop-demo
claude --dangerously-skip-permissions
```

```
Create a branch called lab7 from main and switch to it.
```

### Step 1: Run Three Parallel Investigations (Live)

Check your starting context usage:
```
/context
```

Note the numbers. Now type this prompt:
```
I need three things done in parallel:
1. Review src/api/notes.ts for security issues
2. Check if our test coverage is above 80%
3. Search for any TODO comments across the codebase
Use subagents for each.
```

**What to observe:**
- Claude spawns **three subagents**, each with its own context window
- Each subagent works independently on its assigned task
- Results come back as condensed summaries

### Step 2: Measure the Context Impact

When the subagents return, run `/context` again.

**What to observe:**
- Three investigations just happened — security review, coverage check, TODO search — each involving multiple file reads and tool calls
- Your main context grew by only the summaries (~3-5K tokens total), not the full investigations
- If you'd done all three sequentially in the main context, you'd have consumed 10-20x more context
- This is the **Isolate** strategy in action — splitting work across separate contexts to prevent context bloat

### Key Takeaways
- **Subagents are the simplest form of parallelism** — within one session, no coordination overhead, results come back automatically as condensed summaries.
- **Three levels of agent parallelism exist:** (1) Subagents within one session (this lab), (2) Agent Teams coordinating across sessions with shared task lists and messaging, (3) Independent agents on separate machines with git as the synchronization mechanism. Start with subagents.
- **Don't over-parallelize** — subagents have overhead (spawning time, API cost per context). For a quick grep or a single-file check, just ask the main agent directly. Reserve subagents for investigations that would bloat your context: multi-file reads, cross-cutting concerns, or multi-step analysis.
- **There's no documented hard limit on concurrent subagents** — community testing shows ~10 running concurrently before Claude starts queuing the rest. The real constraint is your machine's RAM and CPU.
- **This is the Isolate strategy in action** — one of four context engineering strategies (Write, Select, Compress, Isolate). You'll use all four as you scale.

### Wrap Up

In Claude Code:
```
Commit and push any changes on this branch with a descriptive message, then switch back to main.
```

### Try It Yourself
- Ask Claude to run 2-3 parallel investigations on your own project
- Try mixing investigation types: one for code review, one for test gaps, one for documentation coverage
- Notice how the main context stays clean even after multiple subagent returns

---

## Troubleshooting

### "jq: command not found" (Lab 4)
The hook script needs `jq` to parse JSON input. See the installation instructions in Prerequisites. Without `jq`, the hook silently fails to block commands.

### Tests fail with "'jest' is not recognized" (Any lab)
Run `npm install` in the `agentic-workshop-demo` directory. The clone doesn't include `node_modules/`.

### Auto-memory directory is empty (Lab 1)
**Symptom:** Claude says "Got it, noted" but no `memory/` directory appears under `~/.claude/projects/`. Auto-memory is a gradual rollout — you must opt in. Set the environment variable and restart your terminal:
```bash
# macOS/Linux — add to ~/.bashrc or ~/.zshrc
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=0

# Windows/Git Bash — already in step 7's .bashrc block. Verify with:
echo $CLAUDE_CODE_DISABLE_AUTO_MEMORY   # should print 0
```

### MCP tools not showing up (Lab 3)
- Restart Claude Code after changing `settings.json`
- Verify the path points to the actual `mcp-server.js` file: `node /path/to/browser-snapshot-agent/mcp-server.js` should start without errors (Ctrl+C to stop)
- On Windows, use double backslashes in the JSON path: `"C:\\Users\\...\\mcp-server.js"`
- If Puppeteer/Chromium failed to install, re-run `npm install` in the `browser-snapshot-agent` directory (Windows: try `npm install --legacy-peer-deps`)

### "Spin up a subagent" doesn't work (Labs 2, 7)
Subagent support requires a recent version of Claude Code. Run:
```bash
npm update -g @anthropic-ai/claude-code
```

### Hook doesn't block on Windows (Lab 4)
- The hook script uses bash syntax. Make sure you're running Claude Code from **Git Bash** or **WSL**, not from PowerShell or cmd directly.
- Verify `jq` is accessible from your Git Bash `PATH`.

### Claude says "no workflow runs found" (Lab 2)
Make sure you completed the setup steps: (1) forked the repo, (2) enabled Actions in Settings > Actions > General, (3) pushed a commit to trigger a CI run. Check your fork's Actions tab to confirm at least one run exists.

### "--max-turns: unknown option" (Lab 6)
Update Claude Code to the latest version:
```bash
npm update -g @anthropic-ai/claude-code
```

---

## Quick Reference: Files Used Per Lab

| Lab | Files |
|-----|-------|
| Lab 1: Memory Stack | `CLAUDE.md`, `.claude/rules/api-conventions.md` |
| Lab 2: Skills | `.claude/skills/noteapi-devops/SKILL.md` |
| Lab 3: MCP | `.claude/settings.json` (mcpServers section) |
| Lab 4: Hooks | `.claude/settings.json` (hooks section), `.claude/hooks/block-destructive.sh` |
| Lab 5: Ralph Wiggum Loop | `src/api/notes.ts` (TODOs), `progress.md` |
| Lab 6: Overnight Harness | `plan.md`, `feature_list.json`, `AGENT_PROMPT.md`, `progress.md`, `lab6-harness.sh` |
| Lab 7: Subagents | `src/api/notes.ts`, `tests/` |

All files are in your forked `agentic-workshop-demo/` directory.
