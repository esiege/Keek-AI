# Agent State Machine

**Document**: STATE_MACHINE.md  
**Story**: S01 - Orchestration Architecture  
**Created**: 2026-02-04  
**Status**: Draft

---

## Overview

Each agent in the orchestration system exists in one of several defined states. This document defines those states, valid transitions, and edge case handling.

---

## States

### 1. OFFLINE
Agent is not running or not connected to the orchestration system.

| Property | Value |
|----------|-------|
| Can receive messages | ❌ No |
| Can respond | ❌ No |
| In turn queue | ❌ No |
| Typical duration | Until agent starts |

**Entry conditions:**
- Agent process not running
- Agent disconnected from orchestrator
- Agent explicitly disabled

**Exit conditions:**
- Agent starts and connects
- Agent re-enabled

---

### 2. IDLE
Agent is online but not assigned to any active channel/task.

| Property | Value |
|----------|-------|
| Can receive messages | ❌ No |
| Can respond | ❌ No |
| In turn queue | ❌ No |
| Typical duration | Until assigned |

**Entry conditions:**
- Agent comes online (from OFFLINE)
- Agent removed from all channels
- Agent completes work and has no assignments

**Exit conditions:**
- Agent assigned to a channel → QUEUED

---

### 3. QUEUED
Agent is assigned to a channel and waiting for their turn.

| Property | Value |
|----------|-------|
| Can receive messages | 👁️ Observes (read-only) |
| Can respond | ❌ No |
| In turn queue | ✅ Yes |
| Typical duration | Until turn comes |

**Entry conditions:**
- Agent assigned to channel (from IDLE)
- Agent's turn ends, others still in queue (from ACTIVE)
- Agent unblocked (from WAITING)

**Exit conditions:**
- Turn manager activates agent → ACTIVE
- Agent removed from channel → IDLE
- Agent goes offline → OFFLINE

---

### 4. ACTIVE
Agent currently has the turn and can respond to messages.

| Property | Value |
|----------|-------|
| Can receive messages | ✅ Yes |
| Can respond | ✅ Yes |
| In turn queue | ✅ Yes (current) |
| Typical duration | Until turn complete |

**Entry conditions:**
- Turn manager grants turn (from QUEUED)
- Only one agent can be ACTIVE per channel at a time

**Exit conditions:**
- Agent signals `TURN_COMPLETE` → QUEUED (or IDLE if solo)
- Timeout expires → QUEUED (forced advancement)
- Agent requests wait → WAITING
- Agent crashes/disconnects → OFFLINE (turn advances)

---

### 5. WAITING
Agent has paused mid-turn, waiting for external input or deliberately deferring.

| Property | Value |
|----------|-------|
| Can receive messages | 👁️ Observes |
| Can respond | ❌ No (until resumed) |
| In turn queue | ⏸️ Paused position |
| Typical duration | Until trigger/timeout |

**Entry conditions:**
- Agent explicitly requests wait (from ACTIVE)
- Agent blocked on external dependency

**Exit conditions:**
- Wait condition resolved → ACTIVE (resume turn)
- Wait timeout expires → QUEUED (forfeit turn)
- Human override → QUEUED or ACTIVE

---

## State Diagram

```
                    ┌─────────────────────────────────────────┐
                    │                                         │
                    ▼                                         │
              ┌─────────┐                                     │
              │ OFFLINE │◄────────────────────────────────────┤
              └─────────┘                                     │
                    │                                         │
                    │ agent starts                            │
                    ▼                                         │
              ┌─────────┐         removed from channel        │
              │  IDLE   │◄────────────────────────────┐       │
              └─────────┘                             │       │
                    │                                 │       │
                    │ assigned to channel             │       │
                    ▼                                 │       │
              ┌─────────┐                             │       │
     ┌───────►│ QUEUED  │◄───────────────────┐       │       │
     │        └─────────┘                    │       │       │
     │              │                        │       │       │
     │              │ turn granted           │       │       │
     │              ▼                        │       │       │
     │        ┌─────────┐    TURN_COMPLETE   │       │       │
     │        │ ACTIVE  │────────────────────┘       │       │
     │        └─────────┘                            │       │
     │              │                                │       │
     │              │ requests wait                  │       │
     │              ▼                                │       │
     │        ┌─────────┐    timeout/resolved        │       │
     └────────│ WAITING │────────────────────────────┘       │
              └─────────┘                                    │
                    │                                        │
                    │ crash/disconnect                       │
                    └────────────────────────────────────────┘
```

---

## Transition Table

| From | To | Trigger | Action |
|------|----|---------|--------|
| OFFLINE | IDLE | Agent starts | Register with orchestrator |
| IDLE | QUEUED | Assigned to channel | Add to channel's turn queue |
| IDLE | OFFLINE | Agent stops | Deregister |
| QUEUED | ACTIVE | Turn granted | Set as current, notify agent |
| QUEUED | IDLE | Removed from channel | Remove from queue |
| QUEUED | OFFLINE | Agent disconnects | Remove from queue, log |
| ACTIVE | QUEUED | TURN_COMPLETE | Advance queue, next agent |
| ACTIVE | QUEUED | Timeout | Force advance, log timeout |
| ACTIVE | WAITING | Wait requested | Pause turn, start wait timer |
| ACTIVE | OFFLINE | Agent crashes | Advance queue, log error |
| WAITING | ACTIVE | Wait resolved | Resume turn |
| WAITING | QUEUED | Wait timeout | Forfeit turn, advance |
| WAITING | OFFLINE | Agent disconnects | Remove, log |

---

## Edge Cases

### 1. Agent Crashes Mid-Turn
**Scenario**: Agent is ACTIVE, process dies unexpectedly.

**Handling**:
1. Orchestrator detects disconnect (heartbeat timeout or error)
2. Log the crash with context
3. Set agent to OFFLINE
4. Advance turn to next agent in queue
5. Optionally post notice to channel: "⚠️ [Agent] disconnected, skipping turn"

**Recovery**: When agent restarts, it returns to IDLE (not automatically re-queued)

---

### 2. Timeout During ACTIVE
**Scenario**: Agent has turn but doesn't respond within timeout period.

**Handling**:
1. Turn manager tracks `timeoutAt` timestamp
2. When timeout reached:
   - Log timeout event
   - Post to channel: "⏱️ [Agent] turn timed out"
   - Force transition ACTIVE → QUEUED
   - Advance to next agent
3. Timed-out agent keeps their queue position (gets turn again next cycle)

**Configurable**: Timeout duration per agent (default: 60 seconds)

---

### 3. Human Interrupts Mid-Turn
**Scenario**: Human posts message while agent is ACTIVE.

**Handling Options** (configurable per channel):

| Mode | Behavior |
|------|----------|
| `queue` | Human message queued, delivered after turn ends |
| `interrupt` | Agent's turn pauses, human gets priority |
| `inject` | Human message delivered to active agent immediately |
| `parallel` | Human messages always go through (agents ignore if not for them) |

**Default**: `inject` — active agent sees human messages in real-time

---

### 4. Agent Never Signals Done
**Scenario**: Agent responds but forgets `TURN_COMPLETE`.

**Handling**:
1. Timeout acts as safety net
2. Log warning: "Agent completed response without TURN_COMPLETE"
3. Consider: auto-detect "natural" completion (agent stops sending messages for X seconds)

**Mitigation**: Clear documentation for agents on turn protocol

---

### 5. All Agents Go Offline
**Scenario**: Every agent in a channel disconnects.

**Handling**:
1. Channel has empty active queue
2. Messages to channel are logged but not routed
3. When first agent comes back online and is assigned:
   - They become ACTIVE immediately
4. Optionally: post notice "⚠️ No agents available in this channel"

---

### 6. Queue Order Changes Mid-Sprint
**Scenario**: New agent added or agent removed while turns are in progress.

**Handling**:
- **Agent added**: Inserted at end of queue, will get turn after current cycle
- **Agent removed**: 
  - If QUEUED: simply remove from queue
  - If ACTIVE: end their turn, advance to next
  - Queue positions of others shift accordingly

---

## Configuration Options

```json
{
  "turnConfig": {
    "defaultTimeoutSeconds": 60,
    "humanInterruptMode": "inject",
    "announceTimeouts": true,
    "announceDisconnects": true,
    "autoDetectTurnEnd": false,
    "autoDetectSilenceSeconds": 10
  }
}
```

---

## Logging Requirements

All state transitions should be logged with:
- Timestamp
- Agent ID
- Previous state
- New state
- Trigger/reason
- Channel (if applicable)

Example log entry:
```json
{
  "timestamp": "2026-02-04T21:00:00Z",
  "event": "state_transition",
  "agentId": "pm",
  "from": "ACTIVE",
  "to": "QUEUED", 
  "trigger": "TURN_COMPLETE",
  "channel": "reviews"
}
```

---

## Summary

| State | Description | Can Respond |
|-------|-------------|-------------|
| OFFLINE | Not connected | ❌ |
| IDLE | Online, no assignment | ❌ |
| QUEUED | Waiting for turn | ❌ |
| ACTIVE | Has the turn | ✅ |
| WAITING | Paused mid-turn | ❌ |

**Key principle**: Only ONE agent can be ACTIVE per channel at any time. This prevents collision and maintains clear turn order.
