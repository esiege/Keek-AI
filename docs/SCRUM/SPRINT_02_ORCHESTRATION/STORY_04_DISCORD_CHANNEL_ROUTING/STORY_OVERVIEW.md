# Story 04: Discord Channel Routing

**Story ID**: S04
**Story Points**: 5
**Status**: TODO
**Sprint**: Sprint 2 - Orchestration

---

## User Story

As the **orchestrator**, I want **agents to respond only in channels they're assigned to** so that **work is organized and agents stay in their lanes**.

---

## Description

The "game board" metaphor: Discord channels are like zones on a tactical map. Agents are assigned to channels and only participate where assigned.

This story implements:

1. **Channel Assignment**: Agents know which channels they monitor
2. **Message Filtering**: Only route messages to agents in that channel
3. **Multi-Agent Channels**: When multiple agents share a channel, turn order applies
4. **Visual Presence**: Agents appear appropriately in their channels

---

## Deliverables

1. Channel-to-agent mapping configuration
2. Message routing logic (filter by channel)
3. Documentation of channel setup patterns
4. Optional: Visual indicators (channel topic, pinned message)

---

## Technical Approach

### Channel Configuration

In persona files or separate config:

```json
{
  "channels": {
    "planning": {
      "id": "1234567890",
      "agents": ["pm"],
      "mode": "single"
    },
    "dev-work": {
      "id": "1234567891", 
      "agents": ["dev"],
      "mode": "single"
    },
    "reviews": {
      "id": "1234567892",
      "agents": ["pm", "dev"],
      "mode": "turn-based"
    },
    "admin-chat": {
      "id": "1468824206677381210",
      "agents": ["orchestrator"],
      "mode": "direct"
    }
  }
}
```

### Routing Modes

| Mode | Behavior |
|------|----------|
| `single` | One agent, always responds |
| `turn-based` | Multiple agents, follow turn order |
| `direct` | Human ↔ Orchestrator, no turn rules |
| `broadcast` | All agents see, none respond (observation) |

### Message Flow

```
Discord Message Received
         │
         ▼
┌─────────────────────┐
│ Get Channel Config  │
└─────────────────────┘
         │
         ▼
┌─────────────────────┐
│ Which agents are    │
│ assigned here?      │
└─────────────────────┘
         │
         ▼
    ┌────┴────┐
    │         │
 Single    Turn-Based
    │         │
    ▼         ▼
 Route to   Check whose
 that agent   turn it is
              │
              ▼
           Route to
           active agent
```

---

## Discord Channel Setup

### Recommended Structure for MVP

```
📁 Keek Dev Studio
├── 📝 admin-chat      (private: Jesse + Keek)
├── 📋 planning        (PM workspace)
├── 💻 dev-work        (Dev workspace)
├── 🔍 reviews         (PM + Dev collaboration)
└── 📢 announcements   (broadcast, no responses)
```

### Channel Creation

Either:
- Manual: Jesse creates channels in Discord
- Automated: Bot creates channels via API (already have permissions)

---

## Dependencies

- Story 02 (Personas) — need agent channel assignments
- Story 03 (Turn Manager) — for multi-agent channel handling

---

## Open Questions

1. Should agents be able to "move" between channels dynamically?
2. How do we handle DMs (direct messages to bot)?
3. Do we need channel-specific prompts/context for agents?
