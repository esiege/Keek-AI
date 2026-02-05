# Message Flow

**Document**: MESSAGE_FLOW.md  
**Story**: S01 - Orchestration Architecture  
**Created**: 2026-02-04  
**Status**: Draft

---

## Overview

This document describes how messages flow through the orchestration system using sequence diagrams. Understanding these flows is critical for implementing the turn manager and debugging issues.

---

## 1. Basic Message Flow

User posts a message → Active agent responds.

```
┌─────────┐     ┌─────────┐     ┌─────────────┐     ┌───────────┐     ┌─────────┐
│  User   │     │ Discord │     │  OpenClaw   │     │Turn Manager│     │  Agent  │
└────┬────┘     └────┬────┘     └──────┬──────┘     └─────┬─────┘     └────┬────┘
     │               │                 │                  │                │
     │ Post message  │                 │                  │                │
     │──────────────>│                 │                  │                │
     │               │                 │                  │                │
     │               │ Webhook/Event   │                  │                │
     │               │────────────────>│                  │                │
     │               │                 │                  │                │
     │               │                 │ Get channel config               │
     │               │                 │─────────────────>│                │
     │               │                 │                  │                │
     │               │                 │ Return: agents,  │                │
     │               │                 │ mode, active     │                │
     │               │                 │<─────────────────│                │
     │               │                 │                  │                │
     │               │                 │ Route to active agent             │
     │               │                 │─────────────────────────────────>│
     │               │                 │                  │                │
     │               │                 │                  │    Process     │
     │               │                 │                  │    message     │
     │               │                 │                  │       ┌───┐    │
     │               │                 │                  │       │   │    │
     │               │                 │                  │       └───┘    │
     │               │                 │                  │                │
     │               │                 │ Agent response   │                │
     │               │                 │<─────────────────────────────────│
     │               │                 │                  │                │
     │               │ Post response   │                  │                │
     │               │<────────────────│                  │                │
     │               │                 │                  │                │
     │ See response  │                 │                  │                │
     │<──────────────│                 │                  │                │
     │               │                 │                  │                │
```

### Steps

1. **User posts** message to Discord channel
2. **Discord** delivers message to OpenClaw via webhook/gateway
3. **OpenClaw** queries Turn Manager for channel config
4. **Turn Manager** returns: which agents, what mode, who's active
5. **OpenClaw** routes message to the active agent's session
6. **Agent** processes message and generates response
7. **Response** flows back through OpenClaw to Discord
8. **User** sees the response in channel

---

## 2. Turn Handoff Flow

Agent A completes turn → Agent B becomes active.

```
┌─────────┐     ┌─────────────┐     ┌───────────┐     ┌─────────┐     ┌─────────┐
│ Agent A │     │  OpenClaw   │     │Turn Manager│     │  State  │     │ Agent B │
└────┬────┘     └──────┬──────┘     └─────┬─────┘     └────┬────┘     └────┬────┘
     │                 │                  │                │                │
     │ Response with   │                  │                │                │
     │ TURN_COMPLETE   │                  │                │                │
     │────────────────>│                  │                │                │
     │                 │                  │                │                │
     │                 │ Detect signal    │                │                │
     │                 │─────────────────>│                │                │
     │                 │                  │                │                │
     │                 │                  │ Update state:  │                │
     │                 │                  │ A → QUEUED     │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │                  │ Advance queue  │                │
     │                 │                  │ index          │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │                  │ Update state:  │                │
     │                 │                  │ B → ACTIVE     │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │                  │ Log turn       │                │
     │                 │                  │ transition     │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │ Turn granted     │                │                │
     │                 │ notification     │                │                │
     │                 │─────────────────────────────────────────────────>│
     │                 │                  │                │                │
     │                 │                  │        [Agent B now active]    │
     │                 │                  │                │                │
```

### Steps

1. **Agent A** includes `TURN_COMPLETE` in their response
2. **OpenClaw** detects the turn completion signal
3. **Turn Manager** receives notification
4. **State** updated: Agent A → QUEUED
5. **Queue index** advanced to next agent
6. **State** updated: Agent B → ACTIVE
7. **Transition** logged to history
8. **Agent B** optionally notified they now have the turn

### Signal Detection

The `TURN_COMPLETE` signal can appear:
- At the end of message: `"Here's my response. TURN_COMPLETE"`
- On its own line: `"...\n\nTURN_COMPLETE"`
- As the entire message: `"TURN_COMPLETE"`

Detection regex: `/TURN_COMPLETE\s*$/`

---

## 3. Timeout Flow

Agent doesn't respond within timeout period.

```
┌─────────┐     ┌───────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│ Agent A │     │Turn Manager│     │  State  │     │ Discord │     │ Agent B │
└────┬────┘     └─────┬─────┘     └────┬────┘     └────┬────┘     └────┬────┘
     │                │                │                │                │
     │     [Agent A is ACTIVE, timer started]          │                │
     │                │                │                │                │
     │                │ Check timeout  │                │                │
     │                │ (periodic)     │                │                │
     │                │───────────────>│                │                │
     │                │                │                │                │
     │                │ timeoutAt      │                │                │
     │                │ exceeded!      │                │                │
     │                │<───────────────│                │                │
     │                │                │                │                │
     │                │ Force state:   │                │                │
     │                │ A → QUEUED     │                │                │
     │                │───────────────>│                │                │
     │                │                │                │                │
     │                │ Post timeout   │                │                │
     │                │ notice         │                │                │
     │                │───────────────────────────────>│                │
     │                │                │                │                │
     │                │       "⏱️ Agent A timed out"    │                │
     │                │                │                │                │
     │                │ Advance queue, │                │                │
     │                │ B → ACTIVE     │                │                │
     │                │───────────────>│                │                │
     │                │                │                │                │
     │                │ Notify Agent B │                │                │
     │                │─────────────────────────────────────────────────>│
     │                │                │                │                │
```

### Steps

1. **Agent A** is ACTIVE with timeout timer running
2. **Turn Manager** periodically checks if `timeoutAt` exceeded
3. **Timeout detected** — Agent A didn't respond in time
4. **Force transition**: Agent A → QUEUED
5. **Post notice** to Discord channel (optional, configurable)
6. **Advance queue** to Agent B
7. **Agent B** becomes ACTIVE

### Timeout Configuration

```json
{
  "turnBehavior": {
    "timeoutSeconds": 60,
    "announceTimeouts": true,
    "timeoutMessage": "⏱️ {agent} turn timed out, moving to next agent"
  }
}
```

---

## 4. Human Interrupt Flow

Human posts while an agent is mid-turn.

### Mode: `inject` (Default)

Human message delivered to active agent immediately.

```
┌─────────┐     ┌─────────┐     ┌─────────────┐     ┌─────────┐
│  Human  │     │ Discord │     │  OpenClaw   │     │ Agent A │
└────┬────┘     └────┬────┘     └──────┬──────┘     └────┬────┘
     │               │                 │                  │
     │   [Agent A is ACTIVE, processing previous msg]    │
     │               │                 │                  │
     │ Post message  │                 │                  │
     │──────────────>│                 │                  │
     │               │                 │                  │
     │               │ Message event   │                  │
     │               │────────────────>│                  │
     │               │                 │                  │
     │               │                 │ Mode = inject    │
     │               │                 │ Route to Agent A │
     │               │                 │─────────────────>│
     │               │                 │                  │
     │               │                 │    Agent sees    │
     │               │                 │    human msg     │
     │               │                 │    mid-turn      │
     │               │                 │                  │
```

### Mode: `queue`

Human message held until current turn ends.

```
┌─────────┐     ┌───────────┐     ┌─────────┐
│  Human  │     │Turn Manager│     │  Queue  │
└────┬────┘     └─────┬─────┘     └────┬────┘
     │                │                │
     │ Post message   │                │
     │───────────────>│                │
     │                │                │
     │                │ Mode = queue   │
     │                │ Hold message   │
     │                │───────────────>│
     │                │                │
     │        [Wait for turn end]      │
     │                │                │
     │                │ Turn ends      │
     │                │ Deliver queued │
     │                │<───────────────│
     │                │                │
```

### Mode: `interrupt`

Human message pauses agent turn, gets immediate response.

```
┌─────────┐     ┌───────────┐     ┌─────────┐     ┌─────────────┐
│  Human  │     │Turn Manager│     │ Agent A │     │ Orchestrator│
└────┬────┘     └─────┬─────┘     └────┬────┘     └──────┬──────┘
     │                │                │                  │
     │ Post message   │                │                  │
     │───────────────>│                │                  │
     │                │                │                  │
     │                │ Mode=interrupt │                  │
     │                │ Pause Agent A  │                  │
     │                │───────────────>│                  │
     │                │                │                  │
     │                │      A → WAITING                  │
     │                │                │                  │
     │                │ Route to       │                  │
     │                │ orchestrator   │                  │
     │                │───────────────────────────────────>
     │                │                │                  │
     │                │ Orchestrator   │                  │
     │                │ responds       │                  │
     │                │<───────────────────────────────────
     │                │                │                  │
     │                │ Resume Agent A │                  │
     │                │───────────────>│                  │
     │                │                │                  │
     │                │      A → ACTIVE                   │
     │                │                │                  │
```

---

## 5. Error Recovery Flow

Agent crashes or disconnects mid-turn.

```
┌─────────┐     ┌─────────────┐     ┌───────────┐     ┌─────────┐     ┌─────────┐
│ Agent A │     │  OpenClaw   │     │Turn Manager│     │  State  │     │ Agent B │
└────┬────┘     └──────┬──────┘     └─────┬─────┘     └────┬────┘     └────┬────┘
     │                 │                  │                │                │
     │  [CRASH]        │                  │                │                │
     │     ╳           │                  │                │                │
     │                 │                  │                │                │
     │                 │ Detect           │                │                │
     │                 │ disconnect       │                │                │
     │                 │─────────────────>│                │                │
     │                 │                  │                │                │
     │                 │                  │ Log error      │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │                  │ A → OFFLINE    │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │                  │ Remove A from  │                │
     │                 │                  │ queue          │                │
     │                 │                  │───────────────>│                │
     │                 │                  │                │                │
     │                 │ Post error       │                │                │
     │                 │ notice           │                │                │
     │                 │<─────────────────│                │                │
     │                 │                  │                │                │
     │                 │                  │ B → ACTIVE     │                │
     │                 │                  │─────────────────────────────────>
     │                 │                  │                │                │
     │                 │            [System continues]     │                │
     │                 │                  │                │                │
```

### Recovery Steps

1. **Crash detected** via heartbeat failure or error
2. **Log error** with context (what agent was doing)
3. **Set agent OFFLINE** in state
4. **Remove from queue** temporarily
5. **Post notice** to channel (optional): "⚠️ Agent A disconnected"
6. **Advance to next agent** — system continues
7. When agent restarts, it returns to IDLE (manual re-assignment needed)

---

## 6. Multi-Agent Collaboration Flow

PM and Dev working together on a task.

```
┌─────────┐     ┌─────────┐     ┌───────────┐     ┌─────────┐     ┌─────────┐
│  Human  │     │   PM    │     │Turn Manager│     │   Dev   │     │ Discord │
└────┬────┘     └────┬────┘     └─────┬─────┘     └────┬────┘     └────┬────┘
     │               │                │                │                │
     │ "Build /hello │                │                │                │
     │  command"     │                │                │                │
     │───────────────────────────────────────────────────────────────>│
     │               │                │                │                │
     │               │    [PM is first in queue, becomes ACTIVE]       │
     │               │                │                │                │
     │               │ Route to PM    │                │                │
     │               │<───────────────│                │                │
     │               │                │                │                │
     │               │ "Here's the    │                │                │
     │               │  spec...       │                │                │
     │               │  TURN_COMPLETE"│                │                │
     │               │───────────────────────────────────────────────>│
     │               │                │                │                │
     │               │                │ PM → QUEUED    │                │
     │               │                │ Dev → ACTIVE   │                │
     │               │                │───────────────>│                │
     │               │                │                │                │
     │               │                │ "Got it, here's│                │
     │               │                │  the code...   │                │
     │               │                │  TURN_COMPLETE"│                │
     │               │                │                │───────────────>│
     │               │                │                │                │
     │               │                │ Dev → QUEUED   │                │
     │               │                │ PM → ACTIVE    │                │
     │               │                │<───────────────│                │
     │               │                │                │                │
     │               │ Route to PM    │                │                │
     │               │<───────────────│                │                │
     │               │                │                │                │
     │               │ "Looks good,   │                │                │
     │               │  approved!     │                │                │
     │               │  TURN_COMPLETE"│                │                │
     │               │───────────────────────────────────────────────>│
     │               │                │                │                │
     │         [Cycle continues or task marked done]   │                │
     │               │                │                │                │
```

---

## Summary Table

| Flow | Trigger | Key Actions |
|------|---------|-------------|
| Basic Message | User posts | Route to active agent, get response |
| Turn Handoff | TURN_COMPLETE | Update state, advance queue, notify next |
| Timeout | Timer expires | Force transition, post notice, advance |
| Human Interrupt | Human posts mid-turn | Depends on mode (inject/queue/interrupt) |
| Error Recovery | Agent crashes | Log, mark offline, advance queue |
| Collaboration | Multi-agent channel | Round-robin turns between PM and Dev |

---

## Implementation Notes

### OpenClaw Integration Points

1. **Message Receive**: Hook into Discord channel message events
2. **Session Routing**: Use `sessions_send` to route to agent sessions
3. **Signal Detection**: Parse agent responses for TURN_COMPLETE
4. **State Persistence**: Read/write orchestration-state.json

### Timing Considerations

- Turn timeout check: poll every 5-10 seconds
- State file writes: atomic (write to temp, rename)
- Message routing: < 100ms latency goal
- Discord rate limits: respect 5 msg/5s per channel
