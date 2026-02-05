# Story 03: Build Turn Manager

**Story ID**: S03
**Story Points**: 8
**Status**: Done
**Sprint**: Sprint 2 - Orchestration

---

## Tasks

| Task | Description | Status |
|------|-------------|--------|
| TASK_01 | Implement State File Management | Done |
| TASK_02 | Implement Turn Queue Logic | Done |
| TASK_03 | Implement Turn Signal Detection | Done |
| TASK_04 | Write Implementation Spec | Done |

---

## User Story

As the **orchestrator**, I want a **turn manager that controls which agent acts when** so that **agents collaborate without talking over each other**.

---

## Description

This is the core logic of the orchestration system. The Turn Manager:

1. **Maintains Turn Queue**: Knows who's next
2. **Tracks Agent States**: Who's idle, active, waiting
3. **Handles Turn Signals**: Recognizes when a turn is complete
4. **Routes Messages**: Sends incoming messages to the active agent
5. **Manages Timeouts**: Handles agents that don't respond

---

## Deliverables

1. Turn Manager implementation (script, service, or integrated with OpenClaw)
2. State file management (`orchestration-state.json`)
3. Turn signal detection (keyword, timeout, or both)
4. Basic round-robin algorithm
5. Logging for debugging turn flow

---

## Technical Approach

### Turn Queue Logic

```
Initial Queue: [PM, Dev]
Current Index: 0

Turn 1: PM is ACTIVE
  → PM receives message
  → PM responds
  → PM signals TURN_COMPLETE
  → Index moves to 1

Turn 2: Dev is ACTIVE
  → Dev receives message
  → Dev responds
  → Dev signals TURN_COMPLETE
  → Index wraps to 0

Turn 3: PM is ACTIVE
  → ... (cycle continues)
```

### State File Structure

```json
{
  "currentTurn": {
    "agentId": "pm",
    "startedAt": "2026-02-04T20:45:00Z",
    "timeoutAt": "2026-02-04T20:46:00Z"
  },
  "queue": ["pm", "dev"],
  "queueIndex": 0,
  "agents": {
    "pm": { "state": "ACTIVE", "lastAction": "2026-02-04T20:45:30Z" },
    "dev": { "state": "QUEUED", "lastAction": "2026-02-04T20:44:00Z" }
  },
  "history": [
    { "agentId": "dev", "startedAt": "...", "endedAt": "...", "reason": "COMPLETE" }
  ]
}
```

### Turn Completion Signals

**Option A: Keyword**
- Agent includes `TURN_COMPLETE` in message
- Explicit, clear, easy to parse
- Risk: Agent forgets to include it

**Option B: Timeout**
- Turn ends after X seconds of silence
- Automatic, no agent action needed
- Risk: Premature end if agent is "thinking"

**Option C: Hybrid (Recommended)**
- Keyword signals immediate completion
- Timeout as fallback safety net
- Best of both worlds

---

## Implementation Options

### Option 1: OpenClaw Cron + State File
- Use OpenClaw's cron/heartbeat to poll state
- State file drives decisions
- Lightweight, uses existing infrastructure

### Option 2: Custom Script/Service
- Separate Node.js script watches Discord
- More control, more complexity
- Might duplicate OpenClaw functionality

### Option 3: OpenClaw Sessions
- Each agent is an OpenClaw session
- Use `sessions_send` to route messages
- Native to OpenClaw, cleaner integration

**Recommendation**: Start with Option 3 (OpenClaw Sessions) if feasible

---

## Dependencies

- Story 01 (Architecture) — need state machine design
- Story 02 (Personas) — need persona definitions to load

---

## Open Questions

1. Can OpenClaw spawn multiple sessions that share Discord visibility?
2. How do we prevent race conditions if messages come fast?
3. Should Turn Manager be "me" (Keek) or a separate orchestrator persona?
