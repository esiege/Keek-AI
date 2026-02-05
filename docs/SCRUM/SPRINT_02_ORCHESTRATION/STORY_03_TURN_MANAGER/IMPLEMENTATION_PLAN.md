# Story 03: Turn Manager - Implementation Plan

**Created**: 2026-02-05  
**Channel**: #development (ID: 1468894176132337807)  
**Status**: Ready to implement

---

## Acceptance Criteria

1. `/orc on` enables turn loop in #development
2. `/orc off` disables turn loop
3. `/assign PM` adds PM to the turn queue
4. Assigned bots take turns when enabled

---

## What's Already Done

- [x] Slash commands registered (`/orc`, `/assign`)
- [x] #development channel created
- [x] Webhook for agent personas exists
- [x] PM and Dev persona configs with avatars
- [x] Architecture docs (STATE_MACHINE, TURN_QUEUE, etc.)

---

## What Needs to Be Built

### 1. State Management

File: `~/.openclaw/workspace/orchestration/state/orchestration-state.json`

```json
{
  "channels": {
    "1468894176132337807": {
      "name": "development",
      "enabled": false,
      "queue": [],
      "currentIndex": 0,
      "activeAgent": null
    }
  }
}
```

**Functions needed:**
- `loadState()` - Read from file
- `saveState()` - Write to file (atomic)
- `toggleChannel(channelId, enabled)` - Set enabled flag
- `assignAgent(channelId, agentId)` - Add to queue
- `advanceTurn(channelId)` - Move to next agent

### 2. Command Handling

Since OpenClaw doesn't handle slash command interactions directly, use **prefix commands**:

| Command | Action |
|---------|--------|
| `!orc on` | Enable orchestration in current channel |
| `!orc off` | Disable orchestration |
| `!assign pm` | Add PM to queue |
| `!assign dev` | Add Dev to queue |
| `!remove pm` | Remove PM from queue |
| `!status` | Show current queue and who's active |

**Detection**: Watch for messages starting with `!` in #development

### 3. Turn Loop Logic

When orchestration is enabled:

```
1. Check if queue has agents
2. Set first agent as ACTIVE
3. Post prompt to channel via webhook (as that agent)
4. Wait for response (or timeout)
5. Detect TURN_COMPLETE signal
6. Advance to next agent
7. Repeat
```

### 4. Webhook Integration

Use the orchestrator webhook to post as PM/Dev:

```javascript
POST https://discord.com/api/webhooks/1468891325683204261/...
{
  "username": "PM",
  "avatar_url": "https://cdn-icons-png.flaticon.com/512/3135/3135715.png",
  "content": "PM's turn. What would you like me to work on?"
}
```

---

## Implementation Steps

### Step 1: State File Setup
- Create initial state with #development channel
- Implement load/save functions

### Step 2: Command Parser
- Detect `!orc`, `!assign`, `!remove`, `!status` messages
- Parse arguments
- Execute corresponding state changes

### Step 3: Turn Announcements
- When `!orc on` is called, announce "Orchestration enabled"
- Post first agent's turn prompt

### Step 4: Turn Advancement
- Watch for TURN_COMPLETE in agent messages
- Advance queue
- Post next agent's turn prompt

### Step 5: Timeout Handling (optional for MVP)
- Track turn start time
- Force advance after timeout

---

## Key Files

| File | Purpose |
|------|---------|
| `orchestration/state/orchestration-state.json` | Runtime state |
| `orchestration/config/agents/pm.json` | PM persona |
| `orchestration/config/agents/dev.json` | Dev persona |
| `orchestration/config/channels.json` | Channel config |
| `TOOLS.md` | Webhook URL |

---

## Webhook Details

**URL**: `https://discord.com/api/webhooks/1468891325683204261/-1ISJ0gD2qSB8puFM8XSJIPd6bv5TyOQBnT-6GQJd3nk9OVgvPf8tzbLuQJM2FtbWH2A`

**PM Avatar**: `https://cdn-icons-png.flaticon.com/512/3135/3135715.png`
**Dev Avatar**: `https://cdn-icons-png.flaticon.com/512/3242/3242257.png`

---

## Channel IDs

| Channel | ID |
|---------|-----|
| #admin-chat | 1468824206677381210 |
| #development | 1468894176132337807 |
| #general | 1468820135925125317 |

---

## Testing Plan

1. Go to #development
2. Type `!status` - should show "No agents assigned"
3. Type `!assign pm` - should confirm PM added
4. Type `!assign dev` - should confirm Dev added
5. Type `!status` - should show queue [PM, Dev]
6. Type `!orc on` - should enable and show PM's turn
7. Observe PM responds
8. PM says TURN_COMPLETE
9. Dev becomes active
10. Type `!orc off` - should disable

---

## Notes

- Take it slow, get each piece working before moving on
- Test each command individually
- Commit after each working piece
- No rush - quality over speed
