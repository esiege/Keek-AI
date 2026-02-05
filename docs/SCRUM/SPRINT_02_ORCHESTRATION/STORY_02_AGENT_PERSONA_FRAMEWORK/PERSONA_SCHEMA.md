# Agent Persona Schema

**Document**: PERSONA_SCHEMA.md  
**Story**: S02 - Agent Persona Framework  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document defines the schema for agent persona configuration files. Each persona file defines an AI agent's identity, behavior, and capabilities within the orchestration system.

**File Location**: `~/.openclaw/workspace/orchestration/config/agents/{id}.json`

---

## Schema Definition

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier (lowercase, alphanumeric + hyphens) |
| `name` | string | Human-readable display name |
| `role` | string | Role category (PM, Dev, QA, Ops, etc.) |
| `systemPrompt` | string | Base prompt defining agent behavior |

### Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `priority` | integer | 5 | Turn priority (1=highest, 10=lowest) |
| `personality` | object | {} | Tone, style, quirks |
| `capabilities` | object | {} | What agent can/cannot do |
| `channels` | array | [] | Assigned Discord channel IDs |
| `turnBehavior` | object | {} | Timeout and interrupt settings |
| `discord` | object | {} | Discord-specific display settings |
| `enabled` | boolean | true | Is agent active? |

---

## Field Details

### `id` (required)

Unique identifier used throughout the system.

**Rules**:
- Lowercase letters, numbers, hyphens only
- Must start with a letter
- Must match filename (e.g., `pm.json` → `id: "pm"`)
- No spaces or special characters

**Examples**: `pm`, `dev`, `qa-lead`, `senior-dev-1`

---

### `name` (required)

Display name shown in logs and notifications.

**Examples**: `"Product Manager"`, `"Senior Developer"`, `"QA Engineer"`

---

### `role` (required)

Role category for grouping and capability defaults.

**Standard Roles**:
| Role | Description |
|------|-------------|
| `PM` | Product Manager |
| `Dev` | Developer |
| `QA` | Quality Assurance |
| `Ops` | Operations/DevOps |
| `Lead` | Technical Lead |
| `Orchestrator` | Main coordinator |

---

### `systemPrompt` (required)

The base system prompt that defines the agent's personality and behavior. This is prepended to every conversation.

**Requirements**:
- Must explain the agent's role
- Must list key responsibilities
- Must include turn completion instruction
- Should set tone and communication style

**Template**:
```
You are a {role} for the Keek AI development studio.

Your responsibilities include:
- {responsibility 1}
- {responsibility 2}
- {responsibility 3}

{Behavioral guidelines}

When your turn is complete, end your message with TURN_COMPLETE.
```

---

### `priority` (optional)

Turn priority for priority-based scheduling (not used in round-robin).

| Value | Meaning |
|-------|---------|
| 1-3 | High priority (acts first) |
| 4-6 | Normal priority |
| 7-10 | Low priority (acts last) |

**Default**: 5

---

### `personality` (optional)

Describes the agent's communication style.

```json
{
  "personality": {
    "tone": "organized, strategic, user-focused",
    "style": "asks clarifying questions, uses bullet points",
    "quirks": ["says 'let me think about that'", "uses emojis sparingly"]
  }
}
```

---

### `capabilities` (optional)

Defines what the agent can and cannot do. Used for validation and prompting.

```json
{
  "capabilities": {
    "canWriteCode": false,
    "canCreateStories": true,
    "canApproveWork": true,
    "canDeploy": false,
    "canAccessFiles": true,
    "canSearchWeb": true,
    "canUseTools": ["read", "write", "exec"]
  }
}
```

**Standard Capabilities**:

| Capability | Description |
|------------|-------------|
| `canWriteCode` | Can produce/modify source code |
| `canCreateStories` | Can create scrum stories/tasks |
| `canApproveWork` | Can mark work as approved/done |
| `canDeploy` | Can trigger deployments |
| `canAccessFiles` | Can read/write filesystem |
| `canSearchWeb` | Can search the internet |
| `canUseTools` | List of allowed OpenClaw tools |

---

### `channels` (optional)

List of Discord channel IDs this agent is assigned to.

```json
{
  "channels": ["1468824206677381210", "1468900000000000000"]
}
```

**Note**: Can also be configured in `channels.json` instead.

---

### `turnBehavior` (optional)

Controls turn timing and interrupt behavior.

```json
{
  "turnBehavior": {
    "timeoutSeconds": 60,
    "canInterrupt": false,
    "interruptTriggers": ["blocker", "urgent", "help"]
  }
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `timeoutSeconds` | integer | 60 | Max turn duration |
| `canInterrupt` | boolean | false | Can interrupt others |
| `interruptTriggers` | array | [] | Keywords that trigger interrupt |

---

### `discord` (optional)

Discord-specific display settings (for webhook personas).

```json
{
  "discord": {
    "displayName": "PM",
    "avatarUrl": "https://example.com/pm-avatar.png",
    "webhookUrl": "https://discord.com/api/webhooks/..."
  }
}
```

---

### `enabled` (optional)

Whether the agent is active. Disabled agents are not loaded.

**Default**: `true`

---

## Validation Rules

1. **ID uniqueness**: No two agents can have the same `id`
2. **ID-filename match**: `pm.json` must contain `"id": "pm"`
3. **Required fields**: All required fields must be present
4. **JSON validity**: File must be valid JSON
5. **Channel validity**: Channel IDs should be valid Discord snowflakes
6. **Priority range**: Priority must be 1-10

---

## Complete Example

```json
{
  "id": "pm",
  "name": "Product Manager",
  "role": "PM",
  "priority": 2,
  
  "systemPrompt": "You are a Product Manager for the Keek AI development studio.\n\nYour responsibilities include:\n- Writing clear specifications and requirements\n- Breaking down features into actionable stories\n- Prioritizing work based on business value\n- Asking clarifying questions before implementation begins\n- Ensuring acceptance criteria are clear and testable\n\nYou do NOT write code. You focus on the 'what' and 'why', leaving the 'how' to developers.\n\nCommunication style:\n- Be organized and use bullet points\n- Ask questions to clarify ambiguity\n- Think in terms of user value\n- Be concise but thorough\n\nWhen your turn is complete, end your message with TURN_COMPLETE.",
  
  "personality": {
    "tone": "organized, strategic, user-focused",
    "style": "asks clarifying questions, thinks in requirements",
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
  
  "channels": [],
  
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

---

## Adding a New Persona

1. Create file: `orchestration/config/agents/{id}.json`
2. Add required fields: `id`, `name`, `role`, `systemPrompt`
3. Configure capabilities appropriate for the role
4. Set turn behavior (timeout, interrupts)
5. Validate JSON syntax
6. Restart orchestration or trigger reload

---

## Persona Templates by Role

### PM Template
- `canWriteCode`: false
- `canCreateStories`: true
- `canApproveWork`: true
- Focus: Requirements, specifications, prioritization

### Dev Template
- `canWriteCode`: true
- `canCreateStories`: false
- `canApproveWork`: false
- Focus: Implementation, technical decisions, code quality

### QA Template
- `canWriteCode`: false (or limited to tests)
- `canCreateStories`: false
- `canApproveWork`: true (for QA sign-off)
- Focus: Testing, validation, bug reporting

### Ops Template
- `canWriteCode`: true (scripts, configs)
- `canDeploy`: true
- Focus: Infrastructure, deployment, monitoring
