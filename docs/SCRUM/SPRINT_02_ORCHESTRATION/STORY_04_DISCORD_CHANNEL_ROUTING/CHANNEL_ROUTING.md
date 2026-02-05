# Channel Routing Specification

**Document**: CHANNEL_ROUTING.md  
**Story**: S04 - Discord Channel Routing  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document specifies how Discord messages are routed to agents based on channel configuration.

---

## Channel Configuration

**Location**: `~/.openclaw/workspace/orchestration/config/channels.json`

### Structure

```json
{
  "version": "1.0",
  "channels": {
    "<channel_id>": {
      "name": "channel-name",
      "mode": "direct|single|turn-based|broadcast",
      "agents": ["agent1", "agent2"],
      "description": "Purpose of channel",
      "humanInterruptMode": "inject|queue|interrupt"
    }
  },
  "defaults": {
    "mode": "direct",
    "humanInterruptMode": "inject"
  }
}
```

---

## Channel Modes

### 1. `direct` Mode

**Purpose**: Human ↔ Orchestrator communication

**Behavior**:
- Single agent (typically orchestrator)
- No turn management
- Agent responds to all messages
- Human messages always delivered immediately

**Use For**: Admin channels, control interface

```json
{
  "mode": "direct",
  "agents": ["orchestrator"]
}
```

---

### 2. `single` Mode

**Purpose**: Dedicated workspace for one agent

**Behavior**:
- Single agent assigned
- No turn management (always active)
- Agent responds to all messages in channel
- Other agents cannot see/respond

**Use For**: PM planning, Dev work, isolated tasks

```json
{
  "mode": "single",
  "agents": ["pm"]
}
```

---

### 3. `turn-based` Mode

**Purpose**: Multi-agent collaboration with turn order

**Behavior**:
- 2+ agents in turn queue
- Only active agent can respond
- TURN_COMPLETE advances to next
- Timeout forces turn advancement

**Use For**: Reviews, collaborative work, handoffs

```json
{
  "mode": "turn-based",
  "agents": ["pm", "dev"]
}
```

---

### 4. `broadcast` Mode

**Purpose**: Observation-only channel

**Behavior**:
- Agents can see messages
- No agent responds
- Useful for announcements, logs
- Humans can post, agents observe

**Use For**: Announcements, status boards

```json
{
  "mode": "broadcast",
  "agents": []
}
```

---

## Routing Decision Flow

```
Message Received
       │
       ▼
┌──────────────────┐
│ Get channel ID   │
└──────────────────┘
       │
       ▼
┌──────────────────┐    No     ┌──────────────────┐
│ Channel in       │─────────▶ │ Use defaults or  │
│ config?          │           │ ignore           │
└──────────────────┘           └──────────────────┘
       │ Yes
       ▼
┌──────────────────┐
│ Get channel mode │
└──────────────────┘
       │
       ├─── direct ────▶ Route to single agent
       │
       ├─── single ────▶ Route to assigned agent
       │
       ├─── turn-based ─▶ Route to ACTIVE agent only
       │
       └─── broadcast ──▶ Log, no response
```

### Pseudocode

```javascript
async function routeMessage(channelId, message, author) {
  // Load channel config
  const config = await loadChannelConfig();
  const channel = config.channels[channelId];
  
  // Unknown channel
  if (!channel) {
    if (config.defaults.ignoreUnknown) {
      console.log(`Ignoring message in unknown channel ${channelId}`);
      return { routed: false, reason: 'unknown_channel' };
    }
    // Use default mode
    channel = { mode: config.defaults.mode, agents: [] };
  }
  
  // Route based on mode
  switch (channel.mode) {
    case 'direct':
    case 'single':
      return routeToAgent(channel.agents[0], channelId, message);
      
    case 'turn-based':
      const activeAgent = await turnManager.getActiveAgent(channelId);
      if (!activeAgent) {
        return { routed: false, reason: 'no_active_agent' };
      }
      return routeToAgent(activeAgent, channelId, message);
      
    case 'broadcast':
      console.log(`Broadcast channel ${channelId}: ${message}`);
      return { routed: false, reason: 'broadcast_mode' };
      
    default:
      console.warn(`Unknown mode ${channel.mode} for ${channelId}`);
      return { routed: false, reason: 'unknown_mode' };
  }
}
```

---

## Human Interrupt Handling

When a human posts while an agent is mid-turn:

| Mode | Behavior |
|------|----------|
| `inject` | Deliver to active agent immediately |
| `queue` | Hold until turn ends, then deliver |
| `interrupt` | Pause agent, handle human, resume |

**Default**: `inject`

---

## Unassigned Channels

When message arrives in channel not in config:

**Options**:
1. **Ignore**: Log and skip
2. **Default route**: Use default mode/agent
3. **Orchestrator**: Route to orchestrator for handling

**Recommended**: Ignore unknown channels in MVP

```javascript
if (!channel && config.defaults.ignoreUnknown) {
  return { routed: false, reason: 'unknown_channel' };
}
```

---

## Adding a New Channel

1. Get Discord channel ID (right-click → Copy ID)
2. Add to channels.json:
   ```json
   "<channel_id>": {
     "name": "new-channel",
     "mode": "turn-based",
     "agents": ["pm", "dev"]
   }
   ```
3. Reload configuration

---

## Error Handling

| Error | Cause | Action |
|-------|-------|--------|
| Unknown channel | Not in config | Ignore or default |
| Empty agents list | Misconfiguration | Log error, skip |
| Agent not found | Invalid agent ID | Log, skip agent |
| Turn manager error | State issue | Log, try recovery |

---

## Summary

| Mode | Agents | Turn Order | Human Messages |
|------|--------|------------|----------------|
| direct | 1 | N/A | Always delivered |
| single | 1 | N/A | Always delivered |
| turn-based | 2+ | Round-robin | Per interrupt mode |
| broadcast | 0+ | N/A | Logged only |
