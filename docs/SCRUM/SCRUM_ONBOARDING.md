# Scrum Onboarding Guide

**Purpose**: Comprehensive reference for agents joining the Keek AI development studio.
**Last Updated**: February 5, 2026

---

# Table of Contents

1. [Overview](#overview)
2. [Core Principles](#core-principles)
3. [Meeting Protocol](#meeting-protocol)
4. [Sprint Cycle](#sprint-cycle)
5. [Story Structure](#story-structure)
6. [Task Workflow](#task-workflow)
7. [Sample Sprint](#sample-sprint)
8. [Sample Story](#sample-story)
9. [Sample Task](#sample-task)
10. [Quick Reference](#quick-reference)

---

# Overview

Keek AI is a single-machine, multi-role AI development studio that orchestrates logical personas to build web applications collaboratively.

## Core Principles

1. **Files as Truth** - Persistent state lives in files, not chat
2. **Discord as Floor** - Status updates, approvals, error surfacing only
3. **Roles Over Humans** - Producer, PM, Dev, QA, Ops as logical personas
4. **Turn-Based Work** - Simulated parallelism through sequential task handoffs
5. **Clear Boundaries** - Dedicated filesystem, single user account, revocable access

## High-Level Architecture

```
Studio Architecture:
Single Machine
  ├── Producer/Orchestrator (routes tasks, tracks state)
  ├── Product Manager (specs, plans)
  ├── Developer (code, features)
  ├── QA Engineer (tests, validation)
  └── Operations (build, deploy)

Communication:
  └── Discord (studio floor - status, approvals, errors)

Persistence:
  └── Filesystem (source of truth - files, not chat)
```

---

# Meeting Protocol

## #scrum Channel Rules

The #scrum channel is for ceremonies (standups, planning, retros). It requires structure.

### During Active Meetings

1. **Facilitator runs the meeting** - Scrum Master or whoever calls it
2. **Speak when called on** - No jumping in
3. **One voice at a time** - Wait for facilitator to pass the floor
4. **Use reactions for quick feedback**:
   - 👍 Agree
   - ❓ I have a question
   - ✋ I want to speak
5. **Stay on topic** - Side discussions go to #development or #admin-chat

### Meeting Flow

1. Facilitator opens with agenda
2. Calls on each participant in turn
3. Participant gives update/input
4. Facilitator acknowledges, moves to next
5. Facilitator closes meeting

### Key Rule

**Don't speak unless spoken to** during ceremonies. Outside of active meetings, channel is open for async coordination.

---

# Sprint Cycle

A sprint is a 1-2 week planning period containing multiple stories, each with tasks.

## Sprint Phases

### 1. Sprint Planning (1-2 hours)
**Goal**: Define stories and plan the sprint

**Activities**:
- Review project backlog
- Estimate story points (using Fibonacci: 1, 2, 3, 5, 8, 13)
- Assign stories to sprint
- Break each story into tasks
- Create story folders and task files
- Set acceptance criteria for each story

### 2. Sprint Execution (1-2 weeks)
**Goal**: Complete stories through their tasks

**Daily Activities**:
- Pick highest priority incomplete task
- Read task description and dependencies
- Implement per task specification
- Test against story's acceptance criteria
- Update task status and log progress
- Commit with story/task reference

### 3. Sprint Review (30-60 minutes)
**Goal**: Demonstrate completed stories and gather feedback

### 4. Sprint Retrospective (30 minutes)
**Goal**: Improve the process

---

# Story Structure

## Story Acceptance Format

When presenting a story for review, use this concise format:

```
**Story [N]: [Title]**

**Description:** [One sentence summary of what was delivered]

**Acceptance Criteria:**
1. [Testable outcome 1]
2. [Testable outcome 2]
3. [Testable outcome 3]

✅ All criteria met and demoed.
```

## Required Files Per Story

1. **STORY_OVERVIEW.md** - Story description, context, technical approach
2. **ACCEPTANCE_CRITERIA.md** - Clear definition of done
3. **STORY_XX_TASK_NN.md** - Individual task files

## Story Status Definitions

| Status | Meaning |
|--------|---------|
| TODO | Story planned but not started |
| IN PROGRESS | Actively being worked on |
| BLOCKED | Cannot proceed |
| IN REVIEW | Code/testing in progress |
| DONE | All acceptance criteria met |
| CANCELLED | Story no longer needed |

---

# Task Workflow

## Workflow Per Task

1. **Read task file** - understand what needs doing
2. **Execute** - run commands, configure, implement
3. **Verify** - confirm task output matches expected outcome
4. **Update status** - mark task done in the file
5. **Commit** - save progress with task reference
6. **Next task** - repeat

## Task Status Updates

Update the task file's status field when:
- Starting: `Not Started` → `In Progress`
- Completing: `In Progress` → `Done`
- Blocked: Add `Blocked: [reason]` note

## Commit Convention

```
feat(STORY_XX): brief description

- Task MM completed
- Details of what was done
```

---

# Sample Sprint

Below is an example of a real sprint overview:

## Sprint 2: Turn-Based Orchestration Foundation

### Sprint Goal
Build the core infrastructure for multi-agent turn-based coordination in Discord, inspired by tactical RPG mechanics (FFT/Tactics Ogre).

### Acceptance Criteria
**Definition of Done**: Two AI agent personas (PM + Dev) successfully take turns responding to a task in a Discord channel, with clear turn handoffs and state tracking.

### Stories

| ID | Story | Points | Status |
|----|-------|--------|--------|
| S01 | Define Orchestration Architecture | 3 | Done ✅ |
| S02 | Create Agent Persona Framework | 5 | Done ✅ |
| S03 | Build Turn Manager | 8 | In Review |
| S04 | Discord Channel Routing | 5 | In Progress |
| S05 | Validate with 2-Agent Demo | 3 | In Progress |
| S06 | Single Dockerized Agent (PoC) | 5 | Done ✅ |
| S07 | Reusable Agent Container Template | 8 | TODO |

**Total Points**: 37

### Core Concepts

**Channel = Context**
- Moving an agent into a channel assigns them to that work
- Channel history provides shared context
- Multiple agents in one channel = collaboration

**Turn = Complete Thought**
- One agent acts at a time (no collision)
- Agent signals "turn complete" explicitly
- State tracked in files, not just chat

---

# Sample Story

Below is an example of a real story:

## Story 01: Define Orchestration Architecture

**Story ID**: S01
**Story Points**: 3
**Status**: Done
**Sprint**: Sprint 2 - Orchestration

### Tasks

| Task | Description | Status |
|------|-------------|--------|
| TASK_01 | Define State Machine | Done |
| TASK_02 | Define Data Structures | Done |
| TASK_03 | Document Message Flow | Done |
| TASK_04 | Write Architecture Doc | Done |

### User Story

As a **studio architect**, I want a **clear documented architecture for turn-based agent orchestration** so that **all future implementation has a solid foundation and shared understanding**.

### Description

Before writing code, we need to design the system. This story produces documentation that defines:

1. **State Machine**: What states can an agent be in? What triggers transitions?
2. **Turn Protocol**: How does turn order work? How are turns signaled complete?
3. **Data Structures**: What does agent config look like? Turn queue? State file?
4. **Message Flow**: How do messages route from Discord → Orchestrator → Agent → Discord?
5. **Extension Points**: Where can we add complexity later (priority, triggers, interrupts)?

### Deliverables

1. `ARCHITECTURE.md` — Full system design document
2. `STATE_MACHINE.md` — Agent state diagram and transitions
3. `DATA_STRUCTURES.md` — JSON schemas for config and state files
4. `MESSAGE_FLOW.md` — Sequence diagrams for key flows

### Technical Approach

```
┌─────────┐    assigned     ┌─────────┐
│  IDLE   │ ──────────────► │ QUEUED  │
└─────────┘                 └─────────┘
     ▲                           │
     │                      turn start
     │                           │
     │                           ▼
     │                      ┌─────────┐
     │  turn complete       │ ACTIVE  │
     └───────────────────── └─────────┘
                                 │
                            blocked/wait
                                 │
                                 ▼
                            ┌─────────┐
                            │ WAITING │
                            └─────────┘
```

---

## Sample Acceptance Criteria

### AC-1: Architecture Document
**GIVEN** the need for multi-agent orchestration
**WHEN** the architecture document is complete
**THEN** it must include:
- [ ] High-level system overview diagram
- [ ] Component responsibilities clearly defined
- [ ] Integration points with OpenClaw identified
- [ ] Integration points with Discord identified

### AC-2: State Machine Definition
**GIVEN** agents need defined behavior states
**WHEN** the state machine document is complete
**THEN** it must include:
- [ ] All possible agent states (minimum: IDLE, QUEUED, ACTIVE, WAITING)
- [ ] All state transitions with triggers
- [ ] State diagram (visual)
- [ ] Edge cases documented (timeout, error, interrupt)

### Definition of Done

- [ ] All four documents created in story folder
- [ ] Diagrams are clear and readable
- [ ] Schemas are valid JSON with comments
- [ ] Team (Jesse) has reviewed and approved
- [ ] No major open questions remaining
- [ ] Documents referenced in sprint README

---

# Sample Task

Below is an example of a real task file:

## Task: Define State Machine

**Story**: 01 - Orchestration Architecture  
**Task ID**: STORY_01_TASK_01  
**Status**: Done

### Objective
Design and document the agent state machine — all possible states and transitions.

### Steps

1. **Define Agent States**
   Document each state an agent can be in:
   - IDLE — not assigned to any active work
   - QUEUED — assigned to channel, waiting for turn
   - ACTIVE — currently has the turn, can respond
   - WAITING — paused mid-turn (blocked, needs input)

2. **Define State Transitions**
   For each state, document:
   - What triggers entry into this state
   - What triggers exit from this state
   - Valid next states

3. **Create State Diagram**
   Visual diagram showing:
   - All states as nodes
   - Transitions as arrows with labels
   - Entry/exit conditions

4. **Document Edge Cases**
   - What if agent crashes mid-turn?
   - What if timeout during ACTIVE?
   - What if human interrupts?
   - What if agent never signals done?

### Expected Outcome
- `STATE_MACHINE.md` document in story folder
- Clear, unambiguous state definitions
- Visual diagram included

### Acceptance Check
- [x] All states defined with descriptions
- [x] All transitions documented
- [x] Diagram created (ASCII or Mermaid)
- [x] Edge cases addressed

### Results (2026-02-04)
Created `STATE_MACHINE.md` with:
- 5 states: OFFLINE, IDLE, QUEUED, ACTIVE, WAITING
- Full transition table with triggers and actions
- ASCII state diagram
- 6 edge cases documented (crash, timeout, interrupt, etc.)
- Configuration options for timeout and interrupt modes
- Logging requirements

---

# Quick Reference

## Story Points

| Points | Size | Time Estimate |
|--------|------|---------------|
| 1 | Trivial | < 1 hour |
| 2 | Simple | 1-2 hours |
| 3 | Small | 2-4 hours |
| 5 | Medium | 4-8 hours |
| 8 | Large | 1-2 days |
| 13 | Very Large | 2-3 days |
| 21+ | Epic | Must be broken down |

## Folder Structure

```
SPRINT_XX_NAME/
├── SPRINT_OVERVIEW.md          # Sprint goal, acceptance criteria, story list
├── STORY_01_NAME/
│   ├── STORY_OVERVIEW.md       # Story description, acceptance criteria
│   ├── ACCEPTANCE_CRITERIA.md  # Definition of done
│   ├── STORY_01_TASK_01_*.md   # Individual task files
│   ├── STORY_01_TASK_02_*.md
│   └── ...
├── STORY_02_NAME/
│   └── ...
└── STORY_03_NAME/
    └── ...
```

## Key Rules

1. **Work happens in files, not chat** - Chat is for coordination and status
2. **Review each story before moving to next** - Quality over speed
3. **Acceptance criteria are the contract** - If not in criteria, not required; if in criteria, must be complete
4. **Complete all tasks in a story before moving to the next**
5. **Stories may have dependencies** - Check STORY_OVERVIEW.md

---

# Success Checklist

### Before Starting a Task
- [ ] Read all story documentation
- [ ] Understand acceptance criteria
- [ ] Review existing patterns
- [ ] Check for dependencies

### While Working
- [ ] Follow existing patterns
- [ ] Update task status as you work
- [ ] Commit frequently
- [ ] Test against acceptance criteria

### After Completing
- [ ] Update task status to Done
- [ ] Verify all acceptance criteria met
- [ ] Mark story as DONE if all tasks complete
- [ ] Move to next task/story

---

**Welcome to the team. Build something amazing.** 👑🔥
