# UK07: Agent Sleep/Wake System

**Status**: TODO  
**Points**: 5  
**Priority**: Medium
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK07_AGENT_SLEEP_WAKE/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

Docker containers for agents run 24/7 even when not being used. This wastes resources (memory, CPU) for idle agents.

## Insight

Agents don't need to be running constantly. They only need to be awake when pinged.

## Target State

### Sleep Mode
- After X minutes of inactivity, agent's Docker container shuts down
- Configurable timeout per agent (some may need longer wake times)
- Graceful shutdown (save state if needed)

### Wake on Ping
- When agent is mentioned/pinged in Discord
- Keek (orchestrator) detects the ping
- Starts the appropriate Docker container
- Routes the message once container is ready
- User sees brief "waking up..." message

### Configuration
```json
{
  "sleep": {
    "enabled": true,
    "inactivityMinutes": 30,
    "wakeMessage": "🧋 Waking up... one sec"
  }
}
```

## Acceptance Criteria

- [ ] Agents automatically sleep after configured inactivity period
- [ ] Ping to sleeping agent triggers wake sequence
- [ ] Wake time is reasonable (< 30 seconds)
- [ ] User gets feedback that agent is waking
- [ ] No messages lost during wake transition

## Context Cost Analysis

**Current cost**: All agents running = all contexts potentially active
**Target cost**: Only active agents consume resources

## Suggestions for Reducing Context Size

1. Sleeping agents have zero context cost
2. Wake only loads minimal context needed for the task
3. Can combine with session cleanup - sleep = archive old context

### Ollama Opportunities (FREE)

- Ollama monitors activity timestamps (no Claude cost)
- Ollama handles "is this agent needed?" routing decisions
- Wake coordination doesn't need expensive model
