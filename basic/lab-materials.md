# Agentic AI Workshop — Basic: Lab Guide
## Building a React Frontend for NoteAPI

[Lab 1](#lab-1-onboard-10-min) | [Lab 2](#lab-2-plan-10-min) | [Lab 3](#lab-3-execute-phase-1--core-components-15-min) | [Lab 4](#lab-4-verify-10-min)

This is a self-paced lab guide for building a React frontend on the existing NoteAPI backend. Each lab builds on the previous one, demonstrating the core agentic AI workflow: onboard → plan → execute → verify.

**Time estimate:** 45-55 minutes total with an agentic AI tool like Claude Code.

---

## Prerequisites

Before starting, ensure you have:

- **Node.js 18+** installed (`node --version` to check)
- **Git** installed and configured
- **Claude Code** (or similar agentic AI tool) installed and configured
- Basic comfort with terminal/command line
- Basic familiarity with TypeScript and React (you don't need to write it yourself)

---

## Setup

### 1. Clone the NoteAPI demo project

```bash
git clone -b basic_workshop_start https://github.com/Apra-Labs/agentic-workshop-demo.git
cd agentic-workshop-demo
```

> **Note:** The `basic_workshop_start` branch has a clean backend-only starting point. If you cloned without `-b`, run `git checkout basic_workshop_start` after cloning.

### 2. Install dependencies and verify the backend works

```bash
npm install
npm test
```

All tests should pass. If they don't, check that you're using Node.js 18+.

### 4. Start the backend server

```bash
npm run dev
```

The server should start on `http://localhost:3000`.

### 5. Verify the API is running

In a new terminal:

```bash
curl http://localhost:3000/api/notes
```

You should see `[]` (empty array) — the backend is running correctly.

**Keep the backend server running throughout the labs.** Open a new terminal for working with the AI agent.

---

## Lab 1: Onboard (~10 min)

**Goal:** Let the agent explore the existing backend and build understanding of what it will extend.

### Why this matters

The agent needs context before it can help. In a real project, you'd point it at an unfamiliar codebase and let it discover the structure, conventions, and key files. This builds the foundation for accurate planning and execution.

### Steps

1. **Open Claude Code in the `agentic-workshop-demo` directory**

   ```bash
   cd agentic-workshop-demo
   claude
   ```

2. **Give the agent the onboarding prompt**

   Copy and paste this prompt exactly:

   ```
   I need you to explore this NoteAPI backend project and understand its structure.

   Please:
   1. Read CLAUDE.md to understand the project basics
   2. Explore the API endpoints in src/api/notes.ts
   3. Review the data model in src/models/note.ts
   4. Check what tests exist and what they verify
   5. Summarize what you learned: API endpoints, data structure, validation rules, and any important conventions

   Don't make any changes yet — just explore and report back.
   ```

3. **Watch the agent explore**

   The agent should read:
   - `CLAUDE.md` — project overview and conventions
   - `package.json` — dependencies and scripts
   - `src/api/notes.ts` — REST endpoints
   - `src/models/note.ts` — Note interface and in-memory store
   - `tests/notes.test.ts` — what the API already tests
   - `src/utils/validation.ts` — input validation logic

4. **Review what the agent discovered**

   The agent should accurately describe:
   - **Endpoints:** GET, POST, PUT, DELETE `/api/notes`
   - **Data model:** `Note { id, title, content, tags[], createdAt, updatedAt }`
   - **Query features:** Filter by tag (`?tag=work`), search (`?q=meeting`)
   - **Validation rules:** Title and content are required, tags must be an array
   - **Test coverage:** CRUD operations, tag filtering, search, validation

5. **Add frontend context to CLAUDE.md**

   Ask the agent:

   ```
   Now that you understand the backend, update CLAUDE.md with a new section explaining that we'll be building a React frontend. Add:
   - The frontend will run on port 3001 (to avoid conflict with the backend on 3000)
   - It should use the existing REST API endpoints
   - Keep it simple: no state management library, just React hooks
   - Follow the same testing approach: Jest tests before features are done
   ```

### Verification

- The agent can describe the API without being told details
- `CLAUDE.md` has a new section about the frontend plan
- You understand what the agent discovered

### What you learned

You just experienced **agent onboarding**. Instead of writing documentation or explaining the codebase, you let the agent explore and build its own understanding. This is faster and more accurate than manually briefing the agent.

### Checkpoint

If you get stuck or want to skip ahead, use:

```bash
git checkout checkpoint/basic-lab1-after-onboard
```

---

## Lab 2: Plan (~10 min)

**Goal:** Co-design a plan for the React frontend with the agent.

### Why this matters

Planning with an agent is collaborative. You provide requirements and constraints, the agent proposes an approach, and you refine it together. The result is `plan.md` — a clear, phased roadmap that guides execution.

### Steps

1. **Describe the requirements to the agent**

   ```
   I want to build a React frontend for this NoteAPI backend.

   Requirements:
   - Display all notes in a list
   - Each note shows title, content, and tags
   - Users can create a new note (form with title, content, tags)
   - Users can edit an existing note
   - Users can delete a note
   - The UI should handle loading and error states
   - Keep it simple: no CSS framework, no state management library, just React hooks

   Please draft a plan.md that breaks this into phases. Each phase should have clear deliverables and be testable.
   ```

2. **Let the agent draft `plan.md`**

   The agent should propose a phased approach (likely 3-4 phases).

3. **Review and push back**

   Read the plan. If the agent proposes Redux, MobX, or another state management library, push back:

   ```
   This is too complex for a workshop demo. Let's keep it simple:
   - Use React hooks (useState, useEffect) for state
   - No state management library
   - Start with the simplest possible components
   - What's the absolute minimum we need for Phase 1?
   ```

4. **Negotiate: "What about error states?" "How should we handle loading?"**

   Ask clarifying questions:

   ```
   Your plan doesn't mention error handling. What if the API request fails?
   What about loading states? Should we show a spinner while fetching notes?
   ```

   The agent should update the plan with error and loading states.

5. **Finalize `plan.md`**

   Once you're satisfied, confirm:

   ```
   This looks good. Save this as plan.md.
   ```

### Verification

- `plan.md` exists in the `agentic-workshop-demo` directory
- The plan is phased (e.g., Phase 1: Core API integration, Phase 2: Create note, etc.)
- Each phase has clear deliverables
- The plan is simple (no over-engineering)

### What you learned

You just experienced **collaborative planning**. The agent proposes, you refine, and you negotiate until the plan matches your constraints. This ensures the agent works on what you actually want, not what it assumes you want.

### Checkpoint

```bash
git checkout checkpoint/basic-lab2-after-plan
```

---

## Lab 3: Execute Phase 1 — Core Components (~15 min)

**Goal:** Let the agent build the basic React components and API integration.

### Why this matters

This is where the agent does the heavy lifting: creating files, writing code, setting up the React app. Your role is to set guardrails and course-correct if it over-engineers.

### Steps

1. **Set guardrails**

   Before execution, establish boundaries:

   ```
   Before we start building, a few rules:
   - Work on a feature branch called "frontend"
   - Commit at meaningful milestones (e.g., "Add React app scaffold", "Add NoteList component")
   - Don't delete or modify the existing backend code or tests
   - If you hit an error, stop and ask me before proceeding
   ```

2. **Start execution with a clear prompt**

   ```
   Let's implement Phase 1 from plan.md.

   Create a React frontend that:
   1. Runs on port 3001 (use VITE or create-react-app, your choice)
   2. Fetches notes from http://localhost:3000/api/notes
   3. Displays them in a simple list (title, content, tags)
   4. Shows a loading state while fetching
   5. Shows an error message if the fetch fails

   Follow the plan exactly. Don't add features that aren't in Phase 1.
   ```

3. **Watch the agent work**

   The agent should:
   - Create a React app (likely using Vite)
   - Set up API integration (fetch notes from the backend)
   - Create a `NoteList` component
   - Handle loading and error states
   - Commit at milestones

4. **Course-correct if it over-engineers**

   If the agent starts adding things not in the plan (e.g., routing, fancy animations, a custom hook library), stop it:

   ```
   Hold on — we don't need routing yet. That's not in Phase 1. Let's stick to the plan.
   ```

5. **Commit when stable**

   Once the agent finishes Phase 1:

   ```
   Great. Run npm start and verify the app works. Then commit with the message "Complete Phase 1: Core components and API integration".
   ```

### Verification

- The React app starts on port 3001 (`npm start` in the frontend directory)
- Opening `http://localhost:3001` shows a list of notes from the backend
- If the backend is stopped, the frontend shows an error message
- While loading, the frontend shows a loading indicator
- Git history shows meaningful commits (not one giant commit)

### What you learned

You just experienced **guided execution**. The agent wrote the code, but you provided direction and boundaries. This is the core workflow: you define the goal, the agent executes, and you course-correct as needed.

### Checkpoint

```bash
git checkout checkpoint/basic-lab3-after-execute-phase1
```

---

## Lab 4: Verify (~10 min)

**Goal:** Human-in-the-loop verification. Confirm the app works end-to-end and the tests are meaningful.

### Why this matters

Agents can write code, but humans verify it works correctly. This step is critical: don't assume the agent got it right. Run the app, test it manually, and read the tests to ensure they're testing real behavior.

### Steps

1. **Run all tests**

   In the frontend directory:

   ```bash
   npm test
   ```

   All tests should pass. If they don't, investigate:

   ```
   The test "renders notes correctly" is failing. The error says "Cannot read property 'map' of undefined". What's going wrong?
   ```

2. **Open the app in the browser**

   Navigate to `http://localhost:3001`.

3. **Create a note manually via the backend**

   In a terminal:

   ```bash
   curl -X POST http://localhost:3000/api/notes \
     -H "Content-Type: application/json" \
     -d '{"title":"Test Note","content":"This is a test.","tags":["test"]}'
   ```

4. **Refresh the frontend — does the new note appear?**

   If it doesn't, check the browser console for errors.

5. **Check: are the tests testing meaningful things?**

   Read the test file (likely `src/NoteList.test.tsx` or similar). Ask yourself:
   - Do the tests verify real behavior (e.g., "renders notes", "shows error on fetch failure")?
   - Or are they shallow (e.g., "component renders without crashing")?

   If the tests are shallow, ask the agent:

   ```
   These tests are too shallow. They don't verify the component actually displays notes from the API. Can you add a test that mocks the fetch and verifies the notes are rendered?
   ```

6. **Document what was accomplished**

   Ask the agent:

   ```
   Create a progress.md file that summarizes what we built in Phase 1:
   - What components were created
   - What API integration was implemented
   - What tests were added
   - What's working and what's not done yet
   ```

### Verification

- `npm test` passes
- Opening the frontend shows notes from the backend
- Tests verify meaningful behavior (not just "renders without crashing")
- `progress.md` exists and accurately describes what was built

### Checkpoint

```bash
git checkout checkpoint/basic-lab4-after-verify
```

### What you learned

You just experienced **human-in-the-loop verification**. The agent executes fast, but you verify the results are correct. This step catches issues early before they compound.

---

## Tips & Troubleshooting

### If the agent gets stuck

- Try `/clear` to reset context, then point it at `plan.md`:

  ```
  Let's start fresh. Read plan.md and continue from where we left off.
  ```

### If the frontend doesn't connect to the API

- **Check CORS:** The backend may need to enable CORS for `http://localhost:3001`. Add this to `src/app.ts`:

  ```typescript
  import cors from 'cors';
  app.use(cors());
  ```

- **Check the port:** Ensure the backend is running on 3000 and the frontend on 3001.

### If tests fail

- **Read the error message carefully.** Don't just re-run the test. Ask the agent:

  ```
  The test failed with this error: [paste error]. What's causing this?
  ```

### If the agent over-engineers

- **Stop it early.** The moment you see it adding things not in the plan, course-correct:

  ```
  We don't need that yet. Stick to the plan.
  ```

### Keep commits small and meaningful

- After each milestone, commit:

  ```
  Good work. Commit this with the message "Add NoteList component with loading state".
  ```

---

## Prompts Reference

For easy copy-paste, here are all the prompts from the labs.

### Lab 1: Onboard

```
I need you to explore this NoteAPI backend project and understand its structure.

Please:
1. Read CLAUDE.md to understand the project basics
2. Explore the API endpoints in src/api/notes.ts
3. Review the data model in src/models/note.ts
4. Check what tests exist and what they verify
5. Summarize what you learned: API endpoints, data structure, validation rules, and any important conventions

Don't make any changes yet — just explore and report back.
```

```
Now that you understand the backend, update CLAUDE.md with a new section explaining that we'll be building a React frontend. Add:
- The frontend will run on port 3001 (to avoid conflict with the backend on 3000)
- It should use the existing REST API endpoints
- Keep it simple: no state management library, just React hooks
- Follow the same testing approach: Jest tests before features are done
```

### Lab 2: Plan

```
I want to build a React frontend for this NoteAPI backend.

Requirements:
- Display all notes in a list
- Each note shows title, content, and tags
- Users can create a new note (form with title, content, tags)
- Users can edit an existing note
- Users can delete a note
- The UI should handle loading and error states
- Keep it simple: no CSS framework, no state management library, just React hooks

Please draft a plan.md that breaks this into phases. Each phase should have clear deliverables and be testable.
```

```
This is too complex for a workshop demo. Let's keep it simple:
- Use React hooks (useState, useEffect) for state
- No state management library
- Start with the simplest possible components
- What's the absolute minimum we need for Phase 1?
```

```
Your plan doesn't mention error handling. What if the API request fails?
What about loading states? Should we show a spinner while fetching notes?
```

```
This looks good. Save this as plan.md.
```

### Lab 3: Execute

```
Before we start building, a few rules:
- Work on a feature branch called "frontend"
- Commit at meaningful milestones (e.g., "Add React app scaffold", "Add NoteList component")
- Don't delete or modify the existing backend code or tests
- If you hit an error, stop and ask me before proceeding
```

```
Let's implement Phase 1 from plan.md.

Create a React frontend that:
1. Runs on port 3001 (use VITE or create-react-app, your choice)
2. Fetches notes from http://localhost:3000/api/notes
3. Displays them in a simple list (title, content, tags)
4. Shows a loading state while fetching
5. Shows an error message if the fetch fails

Follow the plan exactly. Don't add features that aren't in Phase 1.
```

```
Hold on — we don't need routing yet. That's not in Phase 1. Let's stick to the plan.
```

```
Great. Run npm start and verify the app works. Then commit with the message "Complete Phase 1: Core components and API integration".
```

### Lab 4: Verify

```
The test "renders notes correctly" is failing. The error says "Cannot read property 'map' of undefined". What's going wrong?
```

```
These tests are too shallow. They don't verify the component actually displays notes from the API. Can you add a test that mocks the fetch and verifies the notes are rendered?
```

```
Create a progress.md file that summarizes what we built in Phase 1:
- What components were created
- What API integration was implemented
- What tests were added
- What's working and what's not done yet
```

---

## Next Steps (Optional)

If you finish early or want to go deeper:

1. **Implement Phase 2:** Add the "Create Note" form
2. **Implement Phase 3:** Add edit functionality (inline or modal)
3. **Implement Phase 4:** Add delete functionality with confirmation
4. **Add tests for each phase** before moving to the next

Follow the same pattern: plan → execute → verify.

---

## Time Expectations

These labs are designed to be completed in **45-55 minutes** with an agentic AI tool like Claude Code. Without AI, the same tasks would likely take longer (building a React app from scratch, writing all the components, tests, and API integration manually). Your mileage may vary based on familiarity with the tools and whether you encounter setup issues.

The goal isn't speed — it's learning the workflow: onboard, plan, execute, verify. This pattern applies to any project, not just React frontends.

---

## Acknowledgments

This lab guide is part of the **Agentic AI Workshop (Basic)** — a hands-on introduction to working with AI agents for software development. For more workshops and materials, see the main repository.
