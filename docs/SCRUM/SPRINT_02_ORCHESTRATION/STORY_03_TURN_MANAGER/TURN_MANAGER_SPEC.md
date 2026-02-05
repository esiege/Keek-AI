# Turn Manager Implementation Specification

**Document**: TURN_MANAGER_SPEC.md  
**Story**: S03 - Build Turn Manager  
**Created**: 2026-02-04  
**Version**: 1.0  
**Status**: Ready for Implementation

---

## Executive Summary

The Turn Manager is the core coordination component of the orchestration system. It controls which agent can respond at any given time, detects turn completion signals, handles timeouts, and persists state for recovery.

---

## API Reference

### Core Functions

| Function | Description | Returns |
|----------|-------------|---------|
| `initialize()` | Start Turn Manager, load state | `void` |
| `getActiveAgent(channelId)` | Get currently active agent | `string \| null` |
| `advanceTurn(channelId, reason)` | Move to next agent | `TurnResult` |
| `signalComplete(agentId, channelId)` | Agent signals turn done | `TurnResult` |
| `registerAgent(agentId, channelId)` | Add agent to channel | `void` |
| `removeAgent(agentId, channelId)` | Remove agent from channel | `void` |
| `processMessage(channelId, agentId, message)` | Handle agent message | `ProcessResult` |

### Type Definitions

```typescript
interface TurnResult {
  previousAgent: string | null;
  nextAgent: string;
  turnDuration: number;  // seconds
  reason: 'TURN_COMPLETE' | 'TIMEOUT' | 'REMOVED' | 'RECOVERY';
}

interface ProcessResult {
  posted: boolean;
  turnAdvanced: boolean;
  nextAgent?: string;
  reason?: string;
}

interface AgentState {
  state: 'OFFLINE' | 'IDLE' | 'QUEUED' | 'ACTIVE' | 'WAITING';
  lastStateChange: string;  // ISO datetime
  currentChannel: string | null;
  lastActivity: string | null;
}

interface ChannelState {
  queue: string[];
  currentIndex: number;
  activeAgent: string | null;
  turnStartedAt: string | null;
  turnTimeoutAt: string | null;
}
```

---

## Function Specifications

### initialize()

**Purpose**: Start the Turn Manager and prepare for operation.

```javascript
async function initialize() {
  // 1. Load configuration
  config = await loadConfig();
  
  // 2. Load personas
  personas = await loadAllPersonas();
  
  // 3. Load or create state
  state = await loadState();
  
  // 4. Recover from any stale state
  await recoverStaleState(state);
  
  // 5. Start timeout checker
  startTimeoutChecker();
  
  // 6. Register message handlers
  registerMessageHandlers();
  
  console.log('Turn Manager initialized');
}
```

---

### getActiveAgent(channelId)

**Purpose**: Get the agent who currently has the turn.

```javascript
function getActiveAgent(channelId: string): string | null {
  const channel = state.channels[channelId];
  if (!channel) return null;
  return channel.activeAgent;
}
```

**Usage**:
```javascript
const active = getActiveAgent('1468824206677381210');
// Returns: 'pm' or null
```

---

### advanceTurn(channelId, reason)

**Purpose**: Move to the next agent in the turn queue.

**See**: [TURN_QUEUE.md](./TURN_QUEUE.md) for full implementation.

**Key Steps**:
1. Get current channel state
2. Calculate next index (wrap at end)
3. Update previous agent → QUEUED
4. Update next agent → ACTIVE
5. Reset turn timer
6. Log to history
7. Save state

---

### signalComplete(agentId, channelId)

**Purpose**: Process explicit turn completion signal from agent.

```javascript
async function signalComplete(agentId: string, channelId: string): Promise<TurnResult> {
  // Verify agent is active
  const active = getActiveAgent(channelId);
  if (active !== agentId) {
    throw new Error(`${agentId} is not active in ${channelId}`);
  }
  
  // Advance turn
  return await advanceTurn(channelId, 'TURN_COMPLETE');
}
```

---

### registerAgent(agentId, channelId)

**Purpose**: Add an agent to a channel's turn queue.

**See**: [TURN_QUEUE.md](./TURN_QUEUE.md) - `addAgentToQueue()`

---

### removeAgent(agentId, channelId)

**Purpose**: Remove an agent from a channel's turn queue.

**See**: [TURN_QUEUE.md](./TURN_QUEUE.md) - `removeAgentFromQueue()`

---

### processMessage(channelId, agentId, message)

**Purpose**: Handle an agent's message, detect signals, manage turn.

**See**: [SIGNAL_DETECTION.md](./SIGNAL_DETECTION.md) - `processAgentResponse()`

---

## Integration Points

### OpenClaw Integration

**Message Events**:
```javascript
// Hook into OpenClaw's message handling
openclaw.on('message', async (event) => {
  const { channelId, authorId, content, isBot } = event;
  
  // Only process bot (agent) messages for turn management
  if (!isBot) return;
  
  // Map bot ID to agent ID
  const agentId = mapBotToAgent(authorId);
  if (!agentId) return;
  
  // Process through turn manager
  await turnManager.processMessage(channelId, agentId, content);
});
```

**Session Management**:
```javascript
// Spawn agent sessions on initialization
async function spawnAgentSessions() {
  for (const [agentId, persona] of Object.entries(personas)) {
    await sessions_spawn({
      task: persona.systemPrompt,
      label: `agent-${agentId}`
    });
  }
}

// Route messages to agent sessions
async function routeToAgent(agentId, channelId, message) {
  await sessions_send({
    label: `agent-${agentId}`,
    message: `[Channel: ${channelId}]\n${message}`
  });
}
```

### Discord Integration

**Posting Messages**:
```javascript
async function postToDiscord(channelId, agentId, message) {
  // Option 1: Use OpenClaw's message tool
  await message({
    action: 'send',
    channel: 'discord',
    target: channelId,
    message: message
  });
  
  // Option 2: Use webhook for unique agent identity (future)
  // await postViaWebhook(persona.discord.webhookUrl, message);
}
```

**Channel Configuration**:
```javascript
// Load from channels.json
const channelConfig = {
  "1468824206677381210": {
    name: "reviews",
    mode: "turn-based",
    agents: ["pm", "dev"]
  }
};
```

### File System Integration

**Paths**:
```javascript
const PATHS = {
  personas: '~/.openclaw/workspace/orchestration/config/agents/',
  channels: '~/.openclaw/workspace/orchestration/config/channels.json',
  state: '~/.openclaw/workspace/orchestration/state/orchestration-state.json',
  logs: '~/.openclaw/workspace/orchestration/logs/'
};
```

---

## Error Handling

### Error Types

| Error | Cause | Recovery |
|-------|-------|----------|
| `AgentNotFound` | Agent ID doesn't exist | Log, skip operation |
| `ChannelNotFound` | Channel not configured | Log, ignore message |
| `NotActiveAgent` | Wrong agent trying to act | Log warning, ignore |
| `EmptyQueue` | No agents in channel | Log, wait for registration |
| `StateCorrupted` | JSON parse failure | Backup, reset to default |

### Error Handling Pattern

```javascript
async function safeOperation(operation, fallback) {
  try {
    return await operation();
  } catch (error) {
    console.error(`Turn Manager error: ${error.message}`);
    
    addToHistory(state, {
      event: 'error',
      details: { message: error.message, stack: error.stack }
    });
    
    if (fallback) {
      return await fallback();
    }
    
    throw error;
  }
}
```

---

## Logging Requirements

### Log Levels

| Level | Usage |
|-------|-------|
| DEBUG | State changes, routine operations |
| INFO | Turn transitions, agent registration |
| WARN | Non-critical issues (late signal, etc.) |
| ERROR | Failures requiring attention |

### Required Log Points

```javascript
// Turn started
console.log(`[TURN] ${agentId} now active in ${channelId}`);

// Turn completed
console.log(`[TURN] ${agentId} completed turn (${reason}) in ${channelId}`);

// Timeout
console.warn(`[TIMEOUT] ${agentId} timed out after ${duration}s`);

// Error
console.error(`[ERROR] ${operation} failed: ${error.message}`);
```

---

## Implementation Roadmap

### Phase 1: Core State Management
1. ✅ Create state file structure
2. ✅ Implement loadState / saveState
3. ✅ Implement state update helpers
4. ⬜ Test state persistence

### Phase 2: Turn Queue
1. ✅ Implement initializeQueue
2. ✅ Implement advanceTurn
3. ✅ Implement add/remove agent
4. ⬜ Test round-robin behavior

### Phase 3: Signal Detection
1. ✅ Implement TURN_COMPLETE detection
2. ✅ Implement signal stripping
3. ✅ Implement processMessage
4. ⬜ Test signal flow

### Phase 4: Timeout Handling
1. ✅ Implement timeout tracking
2. ✅ Implement checkTimeouts
3. ✅ Implement handleTimeout
4. ⬜ Test timeout behavior

### Phase 5: Integration
1. ⬜ Hook into OpenClaw messages
2. ⬜ Set up cron for timeout checking
3. ⬜ Test with real Discord messages
4. ⬜ End-to-end demo

---

## Testing Strategy

### Unit Tests

```javascript
// test/turn-queue.test.js
describe('Turn Queue', () => {
  test('advanceTurn wraps around', () => {
    // Setup: queue = ['pm', 'dev'], index = 1
    // Action: advanceTurn()
    // Assert: index = 0, activeAgent = 'pm'
  });
  
  test('removing active agent advances turn', () => {
    // Setup: activeAgent = 'pm'
    // Action: removeAgent('pm')
    // Assert: activeAgent = 'dev'
  });
});
```

### Integration Tests

```javascript
// test/turn-manager.integration.test.js
describe('Turn Manager Integration', () => {
  test('full turn cycle PM -> Dev -> PM', async () => {
    // 1. Initialize with PM, Dev
    // 2. PM sends message with TURN_COMPLETE
    // 3. Verify Dev is now active
    // 4. Dev sends message with TURN_COMPLETE
    // 5. Verify PM is now active
  });
});
```

### Manual Testing

See [Story 05 - Two Agent Demo](../STORY_05_TWO_AGENT_DEMO/) for manual test script.

---

## Deployment

### Configuration Checklist

- [ ] Persona files created (`pm.json`, `dev.json`)
- [ ] Channels.json configured
- [ ] State directory exists
- [ ] Timeout values appropriate
- [ ] Discord channel IDs correct

### Startup Checklist

- [ ] Load personas successfully
- [ ] Load/create state file
- [ ] Recover any stale state
- [ ] Start timeout checker
- [ ] Hook message handlers

### Monitoring

- Watch for stuck turns (same agent active too long)
- Watch for error rate in logs
- Monitor state file size (history growth)

---

## Related Documents

| Document | Contents |
|----------|----------|
| [STATE_MANAGEMENT.md](./STATE_MANAGEMENT.md) | State file operations |
| [TURN_QUEUE.md](./TURN_QUEUE.md) | Queue algorithms |
| [SIGNAL_DETECTION.md](./SIGNAL_DETECTION.md) | Signal and timeout handling |
| [ARCHITECTURE.md](../STORY_01_ORCHESTRATION_ARCHITECTURE/ARCHITECTURE.md) | System overview |
| [DATA_STRUCTURES.md](../STORY_01_ORCHESTRATION_ARCHITECTURE/DATA_STRUCTURES.md) | JSON schemas |

---

## Summary

The Turn Manager is fully specified across 4 documents:

1. **STATE_MANAGEMENT.md** - How state is persisted
2. **TURN_QUEUE.md** - How turn order works
3. **SIGNAL_DETECTION.md** - How turns end
4. **TURN_MANAGER_SPEC.md** - How it all fits together (this doc)

**Ready for implementation.** Start with Phase 1 (Core State Management) and work through to Phase 5 (Integration).
