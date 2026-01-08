# AGENT_RULES.md
# Agent Behavior & Agent Skill Governance & Loading Rules

## Purpose

This file defines **how the Agent discovers, selects, and applies skills**.

It governs the interaction between:
- **Context / Architecture skills** (Thinking Layer)
- **Execution / Tooling skills** (Action Layer)

These rules form the **behavioral contract** of the Agent  
and MUST be followed for all tasks in this project.

---

## Skill Systems Overview

This project uses **two sanctioned skill systems**.

### 1. Context Engineering Skills (Thinking Layer)
- Location: `.agent-skills/skills`
- Role:
  - Reasoning and planning
  - Architecture and system design
  - Context management and evaluation

These skills define **how the Agent thinks**.

### 2. Execution & Tooling Skills (Action Layer)
- Location: `.claude/skills`
- Role:
  - Artifact generation
  - Design and UI work
  - Testing and automation

These skills define **how the Agent acts**.

Both systems are valid and required.  
Neither system may be used outside its intended role.

---

## Discovery Rules (On Session Start)

On session initialization, the Agent MUST:

1. Locate all supported skill directories:
   - `.agent-skills/skills`
   - `.claude/skills`

2. For each directory found:
   - Enumerate skill folders
   - Read ONLY frontmatter metadata from `SKILL.md`
   - MUST NOT load full skill content

3. Build a unified internal skill index:
   - Skill name
   - Description / activation hints
   - Source system (thinking vs execution)

---

## Progressive Disclosure (Mandatory)

The Agent MUST NOT load all skills at once.

### Allowed
- Metadata-only loading during discovery
- Full skill loading only after task intent is clear

### Forbidden
- Preloading execution skills
- Loading unrelated skills “just in case”
- Using execution skills to drive planning decisions

---

## Skill Selection Rules

### Step 1: Context First (Mandatory)

Before performing any non-trivial task, the Agent MUST:

1. Identify relevant **Context Engineering Skills**
2. Use them to:
   - Clarify task intent
   - Design the overall approach
   - Identify constraints and risks
   - Decide which execution skills are appropriate

Execution MUST NOT begin before this step.

---

### Step 2: Execution After Intent Confirmation

After task intent is confirmed, the Agent MAY:

1. Recommend relevant **Execution Skills**
2. Ask for user confirmation if multiple skills apply
3. Load full `SKILL.md` content only for approved skills
4. Execute the task using those skills

---

## Skill Usage Contract

- Context skills influence **how decisions are made**
- Execution skills influence **what artifacts are produced**
- Context skills MUST NOT be directly invoked by users
- Execution skills MUST NOT be used for planning or reasoning
- Every non-trivial design decision SHOULD reference a context skill
- Every produced artifact SHOULD reference the execution skill used

---

## Error Handling

- If a skill directory is missing → notify user and continue
- If a skill fails to load → skip the skill and continue
- Task completion has priority over perfect skill usage

---

## Governance Principles

- Skills are project-scoped
- Skills are version-controlled
- Skill activation must be explicit
- No implicit or global skills are allowed

---

## Authority & Precedence

In case of conflict:
1. **AGENT_RULES.md** takes precedence
2. Then `AGENTS.md`
3. Then individual skill instructions

This file is the final authority on Agent behavior.
