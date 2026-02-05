# Data Structures

**Document**: DATA_STRUCTURES.md  
**Story**: S01 - Orchestration Architecture  
**Created**: 2026-02-04  
**Status**: Draft

---

## Overview

This document defines the JSON schemas for all orchestration data structures. These structures are persisted to files and drive the turn-based coordination system.

---

## 1. Agent Configuration

Defines a single agent/persona in the system.

### Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "AgentConfig",
  "type": "object",
  "required": ["id", "name", "role"],
  "properties": {
    "id": {
      "type": "string",
      "description": "Unique identifier (lowercase, no spaces)",
      "pattern": "^[a-z][a-z0-9-]*$"
    },
    "name": {
      "type": "string",
      "description": "Display name for the agent"
    },
    "role": {
      "type": "string",
      "description": "Role category (PM, Dev, QA, Ops, etc.)"
    },
    "priority": {
      "type": "integer",
      "minimum": 1,
      "maximum": 10,
      "default": 5,
      "description": "Turn priority (1=highest, 10=lowest)"
    },
    "systemPrompt": {
      "type": "string",
      "description": "Base system prompt defining agent personality and behavior"
    },
    "personality": {
      "type": "object",
      "properties": {
        "tone": { "type": "string" },
        "style": { "type": "string" },
        "quirks": { "type": "array", "items": { "type": "string" } }
      }
    },
    "capabilities": {
      "type": "object",
      "description": "What the agent can/cannot do",
      "properties": {
        "canWriteCode": { "type": "boolean", "default": false },
        "canCreateStories": { "type": "boolean", "default": false },
        "canApproveWork": { "type": "boolean", "default": false },
        "canDeploy": { "type": "boolean", "default": false },
        "canAccessFiles": { "type": "boolean", "default": true },
        "canSearchWeb": { "type": "boolean", "default": true }
      }
    },
    "channels": {
      "type": "array",
      "items": { "type": "string" },
      "description": "Channel IDs this agent is assigned to"
    },
    "turnBehavior": {
      "type": "object",
      "properties": {
        "timeoutSeconds": { 
          "type": "integer", 
          "default": 60,
          "description": "Max seconds for a turn before timeout"
        },
        "canInterrupt": { 
          "type": "boolean", 
          "default": false,
          "description": "Can this agent interrupt others?"
        },
        "interruptTriggers": { 
          "type": "array", 
          "items": { "type": "string" },
          "description": "Keywords that trigger interrupt (if canInterrupt)"
        }
      }
    },
    "discord": {
      "type": "object",
      "description": "Discord-specific settings",
      "properties": {
        "webhookUrl": { "type": "string", "format": "uri" },
        "avatarUrl": { "type": "string", "format": "uri" },
        "displayName": { "type": "string" }
      }
    },
    "enabled": {
      "type": "boolean",
      "default": true,
      "description": "Is this agent active?"
    }
  }
}
```

### Example: PM Agent

```json
{
  "id": "pm",
  "name": "Product Manager",
  "role": "PM",
  "priority": 2,
  "systemPrompt": "You are a Product Manager for the Keek AI development studio. Your responsibilities include:\n- Writing clear specifications and requirements\n- Breaking down features into stories\n- Prioritizing work based on value\n- Asking clarifying questions before implementation\n- Ensuring acceptance criteria are clear and testable\n\nYou do NOT write code. You focus on the 'what' and 'why', not the 'how'.\n\nWhen your turn is complete, end your message with TURN_COMPLETE.",
  "personality": {
    "tone": "organized, strategic, user-focused",
    "style": "asks clarifying questions, thinks in requirements and user stories",
    "quirks": ["uses bullet points liberally", "always asks 'what problem are we solving?'"]
  },
  "capabilities": {
    "canWriteCode": false,
    "canCreateStories": true,
    "canApproveWork": true,
    "canDeploy": false,
    "canAccessFiles": true,
    "canSearchWeb": true
  },
  "channels": ["1468824206677381210"],
  "turnBehavior": {
    "timeoutSeconds": 90,
    "canInterrupt": false,
    "interruptTriggers": []
  },
  "discord": {
    "displayName": "PM",
    "avatarUrl": null
  },
  "enabled": true
}
```

### Example: Dev Agent

```json
{
  "id": "dev",
  "name": "Developer",
  "role": "Dev",
  "priority": 3,
  "systemPrompt": "You are a Developer for the Keek AI development studio. Your responsibilities include:\n- Implementing features according to specifications\n- Writing clean, documented code\n- Asking clarifying technical questions\n- Suggesting technical approaches\n- Identifying edge cases and potential issues\n\nYou focus on the 'how'. You implement what PM specifies.\n\nWhen your turn is complete, end your message with TURN_COMPLETE.",
  "personality": {
    "tone": "technical, pragmatic, detail-oriented",
    "style": "shows code examples, thinks in systems and patterns",
    "quirks": ["uses code blocks frequently", "mentions edge cases"]
  },
  "capabilities": {
    "canWriteCode": true,
    "canCreateStories": false,
    "canApproveWork": false,
    "canDeploy": false,
    "canAccessFiles": true,
    "canSearchWeb": true
  },
  "channels": ["1468824206677381210"],
  "turnBehavior": {
    "timeoutSeconds": 120,
    "canInterrupt": false,
    "interruptTriggers": []
  },
  "discord": {
    "displayName": "Dev",
    "avatarUrl": null
  },
  "enabled": true
}
```

---

## 2. Channel Configuration

Defines routing and turn rules for a Discord channel.

### Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "ChannelConfig",
  "type": "object",
  "required": ["id", "name", "mode"],
  "properties": {
    "id": {
      "type": "string",
      "description": "Discord channel ID"
    },
    "name": {
      "type": "string",
      "description": "Human-readable channel name"
    },
    "mode": {
      "type": "string",
      "enum": ["single", "turn-based", "direct", "broadcast"],
      "description": "How agents interact in this channel"
    },
    "agents": {
      "type": "array",
      "items": { "type": "string" },
      "description": "Agent IDs assigned to this channel"
    },
    "turnOrder": {
      "type": "string",
      "enum": ["round-robin", "priority", "manual"],
      "default": "round-robin",
      "description": "How turns are assigned"
    },
    "humanInterruptMode": {
      "type": "string",
      "enum": ["queue", "interrupt", "inject", "parallel"],
      "default": "inject",
      "description": "How human messages are handled during agent turns"
    },
    "allowedRoles": {
      "type": "array",
      "items": { "type": "string" },
      "description": "Discord role IDs that can interact (empty = all)"
    }
  }
}
```

### Example: Channel Config

```json
{
  "channels": {
    "admin-chat": {
      "id": "1468824206677381210",
      "name": "admin-chat",
      "mode": "direct",
      "agents": ["orchestrator"],
      "turnOrder": "round-robin",
      "humanInterruptMode": "inject",
      "allowedRoles": []
    },
    "reviews": {
      "id": "1468900000000000000",
      "name": "reviews",
      "mode": "turn-based",
      "agents": ["pm", "dev"],
      "turnOrder": "round-robin",
      "humanInterruptMode": "inject",
      "allowedRoles": []
    },
    "planning": {
      "id": "1468900000000000001",
      "name": "planning",
      "mode": "single",
      "agents": ["pm"],
      "turnOrder": "round-robin",
      "humanInterruptMode": "inject",
      "allowedRoles": []
    }
  }
}
```

---

## 3. Orchestration State

The runtime state of the turn-based system. Persisted to enable recovery after restart.

### Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "OrchestrationState",
  "type": "object",
  "properties": {
    "version": {
      "type": "string",
      "description": "Schema version for migrations"
    },
    "lastUpdated": {
      "type": "string",
      "format": "date-time"
    },
    "agents": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "properties": {
          "state": {
            "type": "string",
            "enum": ["OFFLINE", "IDLE", "QUEUED", "ACTIVE", "WAITING"]
          },
          "lastStateChange": { "type": "string", "format": "date-time" },
          "currentChannel": { "type": "string" },
          "lastActivity": { "type": "string", "format": "date-time" }
        }
      }
    },
    "channels": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "properties": {
          "queue": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Agent IDs in turn order"
          },
          "currentIndex": {
            "type": "integer",
            "description": "Current position in queue"
          },
          "activeAgent": {
            "type": ["string", "null"],
            "description": "Currently active agent ID"
          },
          "turnStartedAt": {
            "type": ["string", "null"],
            "format": "date-time"
          },
          "turnTimeoutAt": {
            "type": ["string", "null"],
            "format": "date-time"
          }
        }
      }
    },
    "history": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "timestamp": { "type": "string", "format": "date-time" },
          "event": { "type": "string" },
          "agentId": { "type": "string" },
          "channelId": { "type": "string" },
          "details": { "type": "object" }
        }
      },
      "description": "Recent turn history (ring buffer, max 100 entries)"
    }
  }
}
```

### Example: Orchestration State

```json
{
  "version": "1.0",
  "lastUpdated": "2026-02-04T21:05:00Z",
  "agents": {
    "pm": {
      "state": "ACTIVE",
      "lastStateChange": "2026-02-04T21:04:30Z",
      "currentChannel": "1468824206677381210",
      "lastActivity": "2026-02-04T21:04:45Z"
    },
    "dev": {
      "state": "QUEUED",
      "lastStateChange": "2026-02-04T21:04:30Z",
      "currentChannel": "1468824206677381210",
      "lastActivity": "2026-02-04T21:03:00Z"
    }
  },
  "channels": {
    "1468824206677381210": {
      "queue": ["pm", "dev"],
      "currentIndex": 0,
      "activeAgent": "pm",
      "turnStartedAt": "2026-02-04T21:04:30Z",
      "turnTimeoutAt": "2026-02-04T21:06:00Z"
    }
  },
  "history": [
    {
      "timestamp": "2026-02-04T21:04:30Z",
      "event": "turn_started",
      "agentId": "pm",
      "channelId": "1468824206677381210",
      "details": { "turnNumber": 1 }
    },
    {
      "timestamp": "2026-02-04T21:03:00Z",
      "event": "turn_completed",
      "agentId": "dev",
      "channelId": "1468824206677381210",
      "details": { "reason": "TURN_COMPLETE", "durationSeconds": 45 }
    }
  ]
}
```

---

## 4. File Locations

Where these files live in the workspace:

```
~/.openclaw/workspace/
├── orchestration/
│   ├── config/
│   │   ├── agents/
│   │   │   ├── pm.json          # PM agent config
│   │   │   ├── dev.json         # Dev agent config
│   │   │   └── ...
│   │   └── channels.json        # Channel configuration
│   ├── state/
│   │   └── orchestration-state.json  # Runtime state
│   └── logs/
│       └── turns.log            # Turn event log
```

---

## 5. Validation Rules

### Agent Config
- `id` must be unique across all agents
- `id` must match filename (e.g., `pm.json` has `id: "pm"`)
- `channels` must reference valid channel IDs
- `priority` must be 1-10

### Channel Config
- `id` must be valid Discord channel ID
- `agents` must reference valid agent IDs
- `mode: "single"` requires exactly 1 agent
- `mode: "turn-based"` requires 2+ agents

### Orchestration State
- `agents` keys must match loaded agent configs
- `channels` keys must match loaded channel configs
- `activeAgent` must be in that channel's `queue`
- `currentIndex` must be valid index into `queue`

---

## Summary

| Structure | Purpose | Location |
|-----------|---------|----------|
| AgentConfig | Define agent personas | `orchestration/config/agents/*.json` |
| ChannelConfig | Define channel routing | `orchestration/config/channels.json` |
| OrchestrationState | Runtime turn state | `orchestration/state/orchestration-state.json` |
