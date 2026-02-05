# Turn-Based Orchestration Architecture

**Document**: ARCHITECTURE.md  
**Story**: S01 - Orchestration Architecture  
**Created**: 2026-02-04  
**Version**: 1.0  
**Status**: Complete

---

## Executive Summary

This document describes the architecture for a turn-based multi-agent orchestration system built on OpenClaw and Discord. Inspired by tactical RPG games (Final Fantasy Tactics, Tactics Ogre), the system enables multiple AI agents to collaborate on tasks by taking turns in a structured, predictable manner.

**Key Innovation**: Treating Discord channels as "battlefields" where agents are deployed, and using explicit turn signals to coordinate who speaks when.

---

## System Overview

### What It Does

The orchestration system enables:
- **Multiple AI personas** (PM, Dev, QA, etc.) to work in shared channels
- **Turn-based coordination** — only one agent responds at a time
- **Explicit handoffs** — agents signal when their turn is complete
- **State persistence** — system resumes correctly after restarts
- **Human integration** — humans can interact alongside agents

### What It Doesn't Do

- Real-time parallel processing (agents work sequentially)
- Autonomous goal pursuit (human oversight required)
- Cross-channel coordination (each channel is independent)
- External system access beyond OpenClaw capabilities

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              DISCORD                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │ #admin-chat │  │  #planning  │  │  #dev-work  │  │  #reviews   │    │
│  │  (direct)   │  │  (single)   │  │  (single)   │  │(turn-based) │    │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘    │
└─────────┼────────────────┼────────────────┼────────────────┼────────────┘
          │                │                │                │
          ▼                ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            OPENCLAW GATEWAY                              │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                        MESSAGE ROUTER                               │ │
│  │  • Receives Discord events                                          │ │
│  │  • Queries Turn Manager for routing decisions                       │ │
│  │  • Dispatches to appropriate agent session                          │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                    │                                     │
│                                    ▼                                     │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                         TURN MANAGER                                │ │
│  │  • Maintains turn queue per channel                                 │ │
│  │  • Tracks agent states (OFFLINE/IDLE/QUEUED/ACTIVE/WAITING)        │ │
│  │  • Detects TURN_COMPLETE signals                                    │ │
│  │  • Handles timeouts and errors                                      │ │
│  │  • Persists state to orchestration-state.json                      │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                    │                                     │
│                    ┌───────────────┼───────────────┐                    │
│                    ▼               ▼               ▼                    │
│  ┌──────────────────────┐ ┌──────────────────────┐ ┌──────────────────┐ │
│  │    AGENT SESSION     │ │    AGENT SESSION     │ │  AGENT SESSION   │ │
│  │    (Orchestrator)    │ │       (PM)           │ │     (Dev)        │ │
│  │  • Main Keek session │ │  • Product specs     │ │  • Code impl     │ │
│  │  • Human interface   │ │  • Requirements      │ │  • Technical     │ │
│  │  • Coordination      │ │  • Prioritization    │ │  • Problem solve │ │
│  └──────────────────────┘ └──────────────────────┘ └──────────────────┘ │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            FILE SYSTEM                                   │
│                                                                          │
│  ~/.openclaw/workspace/orchestration/                                   │
│  ├── config/                                                            │
│  │   ├── agents/           # Agent persona definitions                  │
│  │   │   ├── pm.json                                                    │
│  │   │   └── dev.json                                                   │
│  │   └── channels.json     # Channel routing configuration              │
│  ├── state/                                                             │
│  │   └── orchestration-state.json  # Runtime state (persisted)         │
│  └── logs/                                                              │
│      └── turns.log         # Turn event history                         │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Core Components

### 1. Message Router

**Responsibility**: Receive Discord messages and dispatch to correct agent.

**Inputs**:
- Discord message events (channel ID, content, author)
- Channel configuration (which agents, what mode)
- Turn state (who's active)

**Outputs**:
- Routed message to agent session
- Or ignored (if no agent assigned)

**Key Logic**:
```
ON message_received:
  channel_config = get_channel_config(channel_id)
  
  IF channel_config.mode == "direct":
    route_to(channel_config.agents[0])
    
  ELSE IF channel_config.mode == "single":
    route_to(channel_config.agents[0])
    
  ELSE IF channel_config.mode == "turn-based":
    active_agent = turn_manager.get_active(channel_id)
    route_to(active_agent)
    
  ELSE IF channel_config.mode == "broadcast":
    // No response, agents observe only
    log("Broadcast channel, no routing")
```

### 2. Turn Manager

**Responsibility**: Control turn order and state transitions.

**Core Data**:
- Turn queue per channel (ordered list of agent IDs)
- Agent states (OFFLINE, IDLE, QUEUED, ACTIVE, WAITING)
- Current turn info (who, when started, when times out)

**Key Operations**:

| Operation | Description |
|-----------|-------------|
| `get_active(channel)` | Return currently active agent for channel |
| `advance_turn(channel)` | Move to next agent in queue |
| `signal_complete(agent)` | Agent finished their turn |
| `force_timeout(agent)` | End turn due to timeout |
| `assign_agent(agent, channel)` | Add agent to channel queue |
| `remove_agent(agent, channel)` | Remove agent from channel |

**State Persistence**: All state written to `orchestration-state.json` on every change.

### 3. Agent Sessions

**Responsibility**: Execute agent personas via OpenClaw sessions.

**Options for Implementation**:

| Approach | Pros | Cons |
|----------|------|------|
| Sub-agents (`sessions_spawn`) | Native OpenClaw, isolated | Limited control over prompts |
| Separate OpenClaw instances | Full isolation | Complex setup, more resources |
| Single session, role switching | Simple | Context bleed between roles |
| Webhook personas | Unique Discord identities | Requires webhook setup |

**Recommended**: Start with sub-agents, evaluate webhook personas for visual distinction.

### 4. State Persistence

**Responsibility**: Ensure system can recover after restart.

**Persisted Data**:
- Agent states (who's online, what state)
- Channel queues (turn order)
- Current turn info (who's active, timeout deadline)
- Recent history (last N turn events)

**File**: `orchestration/state/orchestration-state.json`

**Write Strategy**: 
1. Write to temp file
2. Atomic rename to target
3. Prevents corruption on crash

---

## Integration Points

### OpenClaw Integration

| Feature | Usage |
|---------|-------|
| `sessions_spawn` | Create agent sub-sessions |
| `sessions_send` | Route messages to agents |
| `sessions_list` | Monitor agent sessions |
| `cron` | Timeout checking (periodic) |
| Message events | Trigger routing |

### Discord Integration

| Feature | Usage |
|---------|-------|
| Channel messages | Input for routing |
| Bot responses | Agent output |
| Webhooks (optional) | Unique agent identities |
| Reactions | Lightweight feedback |

### File System Integration

| Path | Purpose |
|------|---------|
| `orchestration/config/agents/*.json` | Agent definitions |
| `orchestration/config/channels.json` | Channel routing rules |
| `orchestration/state/*.json` | Runtime state |
| `orchestration/logs/*.log` | Event history |

---

## Extension Points

The architecture supports future enhancements at these points:

### 1. Turn Order Algorithms

Current: Round-robin (simple rotation)

Future options:
- **Priority-based**: Higher priority agents go first
- **Speed-based**: Agents with higher "speed" get more turns
- **Dynamic**: Order changes based on context

**Extension point**: `TurnManager.calculate_next_agent()`

### 2. Interrupt/Reaction System

Current: Strict turn order, no interrupts

Future options:
- **Trigger keywords**: "blocker" causes interrupt
- **Reaction abilities**: Agent can respond to specific events
- **Priority interrupts**: Urgent agents can cut in

**Extension point**: `TurnManager.check_interrupts()`

### 3. Cross-Channel Coordination

Current: Channels are independent

Future options:
- **Shared context**: Agents aware of work in other channels
- **Escalation**: Move tasks between channels
- **Dependencies**: Block on other channel completion

**Extension point**: `Orchestrator.coordinate_channels()`

### 4. Agent Capabilities

Current: Static capabilities per persona

Future options:
- **Tool access control**: Fine-grained permissions
- **Learning**: Agents improve based on feedback
- **Specialization**: Agents develop expertise over time

**Extension point**: `AgentConfig.capabilities`

---

## Glossary

| Term | Definition |
|------|------------|
| **Agent** | An AI persona with defined role and behavior |
| **Turn** | The period when an agent is ACTIVE and can respond |
| **Queue** | Ordered list of agents waiting for their turn |
| **Channel Mode** | How a Discord channel handles agent interaction |
| **TURN_COMPLETE** | Signal keyword indicating agent finished their turn |
| **Orchestrator** | The main Keek session that coordinates agents |
| **Persona** | The configuration defining an agent's personality |

---

## Related Documents

| Document | Description |
|----------|-------------|
| [STATE_MACHINE.md](./STATE_MACHINE.md) | Agent states and transitions |
| [DATA_STRUCTURES.md](./DATA_STRUCTURES.md) | JSON schemas for config and state |
| [MESSAGE_FLOW.md](./MESSAGE_FLOW.md) | Sequence diagrams for message routing |
| [SPRINT_OVERVIEW.md](../SPRINT_OVERVIEW.md) | Sprint goals and stories |

---

## Decision Log

| Decision | Rationale | Date |
|----------|-----------|------|
| Use `TURN_COMPLETE` keyword | Explicit > implicit; easy to parse | 2026-02-04 |
| File-based state persistence | Simple, debuggable, no DB needed | 2026-02-04 |
| Start with round-robin turns | Simple foundation, extend later | 2026-02-04 |
| Channel = independent context | Reduces complexity for MVP | 2026-02-04 |
| Timeout as safety net | Prevents stuck turns | 2026-02-04 |

---

## Open Questions (Resolved)

| Question | Resolution |
|----------|------------|
| One session or multiple? | Multiple sub-agents via `sessions_spawn` |
| How to handle crashes? | Detect disconnect, advance turn, log error |
| Human message handling? | Configurable per channel (inject/queue/interrupt) |
| State persistence format? | JSON file, atomic writes |

---

## Next Steps

With architecture defined, proceed to:

1. **Story 02**: Create agent persona framework (PM + Dev definitions)
2. **Story 03**: Implement Turn Manager core logic
3. **Story 04**: Implement channel routing
4. **Story 05**: Validate with 2-agent demo

---

## Appendix: Quick Reference

### Channel Modes

| Mode | Agents | Turn Order | Use Case |
|------|--------|------------|----------|
| `direct` | 1 | N/A | Human ↔ Orchestrator |
| `single` | 1 | N/A | Dedicated workspace |
| `turn-based` | 2+ | Round-robin | Collaboration |
| `broadcast` | 0+ | N/A | Observation only |

### Agent States

| State | Can Respond | In Queue |
|-------|-------------|----------|
| OFFLINE | ❌ | ❌ |
| IDLE | ❌ | ❌ |
| QUEUED | ❌ | ✅ |
| ACTIVE | ✅ | ✅ |
| WAITING | ❌ | ⏸️ |

### Turn Completion

```
Agent response ending with TURN_COMPLETE:

"Here is my analysis of the requirements.

The key features needed are:
1. User authentication
2. Dashboard view
3. Data export

TURN_COMPLETE"
```
