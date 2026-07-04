---
name: intervention-learning
description: >
  Intervention learning. Use when the human corrects, redirects, repeats an instruction,
  says to remember/update rules, or when another skill wants to turn operator feedback
  into proposed changes for CLAUDE.md, AGENTS.md, CONTEXT.md, rules, checklists, or SKILL.md files.
---

# Intervention Learning

Turn human interventions into approved instruction patches so the same intervention is less likely next time.

An **intervention** is a moment where the human had to correct, redirect, constrain, clarify, or manually compensate for the agent.

Hard invariant: **never modify settings, rules, memory, context, or skill files without explicit human approval of the exact proposed change.**

## Process

### 1. Find interventions

Review the conversation for human messages that changed how the agent should act.

Count as interventions:

- corrections: “That is wrong”, “Don’t do that”, “Use this instead”
- repeated instructions: “I already said…”
- workflow rules: “Always do X before Y”
- persistence requests: “Add this to CLAUDE.md”, “This should be a rule”
- project conventions: paths, commands, naming, review rules, validation steps
- manual operator work that the agent should have done

Do not count ordinary task content, one-off preferences, or facts that will not affect future runs.

Completion criterion: every candidate intervention is either promoted to Step 2 or explicitly discarded as non-durable.

### 2. Distill lessons

For each intervention, write a candidate **lesson** with four fields:

```text
Evidence:
What the human said or did.

Agent miss:
What the agent failed to know, check, ask, or do.

Future behavior:
The specific behavior that would prevent the same intervention.

Scope:
Where the lesson applies, such as this repo, this task type, this skill, or all agent work.
```

A lesson is durable only if it is:

* grounded in conversation evidence
* likely to recur
* actionable as an instruction
* narrow enough to avoid overgeneralization
* compatible with higher-priority instructions
* free of secrets, credentials, and unnecessary personal data

Completion criterion: every lesson has evidence, future behavior, and scope; otherwise discard it.

### 3. Choose the target

Select exactly one primary target for each lesson.

Use this order:

1. existing skill file, if the lesson improves a repeatable workflow
2. `CONTEXT.md`, if the lesson is project-specific context
3. `CLAUDE.md`, `AGENTS.md`, or equivalent, if the lesson is an agent operating rule
4. checklist, runbook, or PR template, if the lesson is validation-focused
5. new skill file, only if the workflow is repeatable and not already covered - using the /write-a-great-skill skill to create it.

Inspect the target file before proposing a patch when the file is available.

Completion criterion: each lesson has one target file, or is marked “needs human target”.

### 4. Draft the patch

Propose the smallest instruction that would have prevented or reduced the intervention.

Good patches are:

* imperative
* specific
* scoped
* testable
* non-duplicative
* easy to remove later

Avoid patches like:

```text
Be careful.
Remember user feedback.
Do better next time.
Use common sense.
```

Prefer patches like:

```text
Before reporting a code-edit task complete, run the repository's existing test command if available. If tests cannot be run, state why and name the unverified risk.
```

Completion criterion: every proposed patch is concrete enough that a future agent can tell whether it followed it.

### 5. Report and ask approval

Before editing anything, present this report:

````markdown
## Intervention Learning Report

### Lesson 1

**Evidence:**  
...

**Agent miss:**  
...

**Future behavior:**  
...

**Scope:**  
...

**Target file:**  
...

**Proposed patch:**

```diff
...
````

**Confidence:** High / Medium / Low

**Why persist:**  
...

**Risk:**  
...

````

Then ask:

```text
Should I apply this update?

1. Apply as proposed.
2. Modify the wording first.
3. Save it to a different file.
4. Do not persist this lesson.
````

If there are multiple independent lessons, approval must be separable per lesson.

Completion criterion: no file is changed until the human explicitly approves the exact change.

### 6. Apply approved patches only

After approval:

* modify only approved files
* preserve existing structure and style
* avoid duplicate rules
* do not remove existing instructions unless explicitly approved
* keep the patch minimal

Then report:

```markdown
## Applied Updates

- `path/to/file`: added/changed ...
```

Completion criterion: every changed file and changed instruction is named.

### 7. Replay check

After applying, perform a **replay check**:

```text
If the same situation happened again, would the new instruction likely prevent the intervention?
```

Answer one of:

* Yes — why
* Partially — what remains
* No — propose a narrower or better patch

Completion criterion: every applied lesson passes or is flagged for follow-up.

## Discard rules

Do not persist a lesson when:

* it is one-off task content
* it is vague
* it is already covered
* it conflicts with higher-priority instructions
* it contains secrets or credentials
* it records personal data without need
* it weakens safety, tests, security, review, or human approval
* it asks the agent to hide behavior or avoid disclosure
* it overgeneralizes from weak evidence

If discarded, say why.

## Conflict handling

If a proposed lesson conflicts with an existing instruction, do not resolve silently.

Use:

```markdown
## Instruction Conflict

**Existing instruction:**  
...

**Proposed lesson:**  
...

**Conflict:**  
...

**Recommended resolution:**  
...

Should I proceed with this resolution?
```

## New skill threshold

Create or propose a new skill only when all are true:

* the workflow is repeatable
* the trigger is recognizable
* the steps are more than a single rule
* the behavior does not belong naturally in an existing skill
* the skill would reduce future interventions

Otherwise prefer a rule, checklist item, or context note.

## Completion

The intervention-learning run is complete when exactly one of these is true:

1. no durable lesson is found, with reasons
2. lessons are proposed and the human declines them
3. lessons are proposed and await approval
4. approved patches are applied and replay-checked