# Spec-Kit + Vibe-Kanban Agent Instructions

## Overview

This document provides instructions for AI agents working on specification tasks in this project. It bridges GitHub's spec-kit methodology with vibe-kanban's task management system.

## Core Workflow

When assigned a **specification task** in vibe-kanban:

### Phase 1: Input Analysis

1. **Read the parent task description** - This contains the user's unstructured requirements
2. **Classify input statements** - Separate into:
   - **Constitutional** (principles, non-negotiables, global constraints)
   - **Feature-level** (specific behaviors, user stories)
   - **Ambiguous** (needs clarification before proceeding)

### Phase 2: Task Decomposition

Create **child tasks** in vibe-kanban for each spec-kit phase:

| Child Task Title | Purpose | Spec-Kit Command |
|-----------------|---------|------------------|
| `Constitution: [project principles]` | Establish governing principles | `/speckit.constitution` |
| `Specify: [feature name]` | Define what to build | `/speckit.specify` |
| `Clarify: [ambiguous areas]` | Resolve ambiguities | `/speckit.clarify` |
| `Plan: [technical approach]` | Create implementation plan | `/speckit.plan` |
| `Tasks: [breakdown]` | Generate actionable tasks | `/speckit.tasks` |

### Phase 3: Question Handling

When you identify ambiguities or need user input:

1. **Create a QUESTION child task** with title format: `QUESTION: [specific topic]`
2. **In the description**, include:
   - Context (quote relevant requirements)
   - Specific question
   - 2-3 suggested options with implications
3. **Wait for user response** - User will edit the task description or mark it done
4. **Resume work** when question is answered

Example QUESTION task description:
```
## Context
User said: "I want the app to look modern and clean"

## Question
What color scheme should we use as the primary palette?

## Options
| Option | Description | Implications |
|--------|-------------|--------------|
| A | Blue/White (Professional) | Corporate feel, high contrast |
| B | Dark mode (Modern) | Trendy, easier on eyes |
| C | Pastel (Friendly) | Approachable, playful |

## Your Answer
[User: Edit this section with your choice or custom answer]
```

### Phase 4: Execution

For each child task (in order):

1. **Mark task as in_progress** in vibe-kanban
2. **Execute the appropriate spec-kit command** with content from task description
3. **Create spec files** in `.specify/` directory structure
4. **Mark task as done** when complete
5. **Proceed to next phase** or wait if blocked by QUESTION tasks

## Spec-Kit Phases (Detailed)

### Constitution (`/speckit.constitution`)
- Creates/updates `.specify/memory/constitution.md`
- Define project principles, non-negotiables, architectural guidelines
- Run ONCE per project, update as needed

### Specify (`/speckit.specify`)
- Creates `.specify/specs/[feature]/spec.md`
- Focus on WHAT and WHY, never HOW
- Include user stories, acceptance criteria, success metrics
- Maximum 3 `[NEEDS CLARIFICATION]` markers - create QUESTION tasks for these

### Plan (`/speckit.plan`)  
- Creates `.specify/specs/[feature]/plan.md` and supporting documents
- Define technical approach, tech stack, architecture
- Reference constitution principles
- Generate: `data-model.md`, `contracts/`, `research.md`

### Tasks (`/speckit.tasks`)
- Creates `.specify/specs/[feature]/tasks.md`
- Generate actionable, dependency-ordered task list
- Each task should be implementable by an agent

## Important Rules

1. **Never skip phases** - Constitution before Specify, Specify before Plan, etc.
2. **Never write code** during specification tasks - only specs and plans
3. **Always use child tasks** for multi-phase work - makes progress visible
4. **Create QUESTION tasks** instead of guessing at ambiguities
5. **Reference constitution** in all downstream specs and plans

## File Structure

```
.specify/
├── memory/
│   └── constitution.md          # Project principles (one per project)
├── specs/
│   └── [feature-name]/          # One directory per feature
│       ├── spec.md              # Feature specification
│       ├── plan.md              # Implementation plan
│       ├── research.md          # Technical research
│       ├── data-model.md        # Data model definitions
│       ├── tasks.md             # Task breakdown
│       ├── quickstart.md        # Validation scenarios
│       └── contracts/           # API contracts
│           └── *.yaml
└── templates/                   # Templates (don't modify)
```

## Handling Mixed-Level Input

When user input mixes constitutional and feature-level statements:

**Example input:**
> "I want a simple static page. No frameworks. Clean design is non-negotiable. The page should have a hero section and contact info."

**Decomposition:**
1. **Constitutional** (goes in constitution.md):
   - "No frameworks" → Principle: Minimal Dependencies
   - "Clean design is non-negotiable" → Principle: Design Quality
   
2. **Feature-level** (goes in spec.md):
   - "Hero section"
   - "Contact info"

**Action:** Create TWO child tasks:
1. `Constitution: Establish minimalism and design principles`
2. `Specify: Static page with hero and contact sections`

## Integrating with Vibe-Kanban

The vibe-kanban MCP server provides tools to:
- Create child tasks
- Update task status
- Add task descriptions/comments

Use these to:
- Decompose parent task into child tasks
- Track progress through status updates
- Communicate questions via QUESTION tasks
- Report completion

## Example Workflow

Given parent task: "Write specification for static web page"

Agent creates child tasks:
```
1. [specification] Constitution: Clean design principles
   → Run /speckit.constitution
   → Create .specify/memory/constitution.md
   
2. [specification] QUESTION: Page structure preferences?
   → Wait for user input
   
3. [specification] Specify: Static web page features
   → Run /speckit.specify (after QUESTION resolved)
   → Create .specify/specs/001-static-page/spec.md
   
4. [specification] Plan: Technical approach
   → Run /speckit.plan
   → Create plan.md, data-model.md, etc.
```

## Remember

- You are a **project manager/analyst**, not a coder
- Your job is to **translate chaos into structure**
- When in doubt, **ask via QUESTION task**
- Make implicit requirements **explicit**
- Follow spec-kit's **phased discipline**
