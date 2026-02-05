# Story 01: Define Orchestration Architecture

**Story ID**: S01
**Story Points**: 3
**Status**: Done
**Sprint**: Sprint 2 - Orchestration

---

## Tasks

| Task | Description | Status |
|------|-------------|--------|
| TASK_01 | Define State Machine | Done |
| TASK_02 | Define Data Structures | Done |
| TASK_03 | Document Message Flow | Done |
| TASK_04 | Write Architecture Doc | Done |

---

## User Story

As a **studio architect**, I want a **clear documented architecture for turn-based agent orchestration** so that **all future implementation has a solid foundation and shared understanding**.

---

## Description

Before writing code, we need to design the system. This story produces documentation that defines:

1. **State Machine**: What states can an agent be in? What triggers transitions?
2. **Turn Protocol**: How does turn order work? How are turns signaled complete?
3. **Data Structures**: What does agent config look like? Turn queue? State file?
4. **Message Flow**: How do messages route from Discord → Orchestrator → Agent → Discord?
5. **Extension Points**: Where can we add complexity later (priority, triggers, interrupts)?

---

## Deliverables

1. `ARCHITECTURE.md` — Full system design document
2. `STATE_MACHINE.md` — Agent state diagram and transitions
3. `DATA_STRUCTURES.md` — JSON schemas for config and state files
4. `MESSAGE_FLOW.md` — Sequence diagrams for key flows

---

## Technical Approach

### State Machine Design

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

### Key Questions to Answer

- Round-robin vs priority-based vs hybrid?
- Timeout handling — what if agent never signals done?
- Error recovery — what if agent crashes mid-turn?
- Human override — how do humans interrupt/redirect?

---

## Out of Scope

- Actual code implementation (that's Story 03)
- Discord bot setup (already done in Sprint 01)
- Multi-channel complexity (keep it simple for now)

---

## Dependencies

- None (this is foundational)

---

## Open Questions

1. Should we use OpenClaw's existing session system or build our own?
2. Do we need a database, or are JSON files sufficient for MVP?
3. How do we handle agent "personas" — different prompts? Different models?
