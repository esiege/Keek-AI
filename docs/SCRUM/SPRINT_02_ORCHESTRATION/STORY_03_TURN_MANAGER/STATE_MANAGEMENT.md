# State Management Specification

**Document**: STATE_MANAGEMENT.md  
**Story**: S03 - Build Turn Manager  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document specifies how orchestration state is managed, persisted, and recovered.

**State File**: `~/.openclaw/workspace/orchestration/state/orchestration-state.json`

---

## State File Structure

```json
{
  "version": "1.0",
  "lastUpdated": "2026-02-04T21:15:00Z",
  "agents": {
    "pm": {
      "state": "QUEUED",
      "lastStateChange": "2026-02-04T21:10:00Z",
      "currentChannel": "1468824206677381210",
      "lastActivity": "2026-02-04T21:12:00Z"
    },
    "dev": {
      "state": "ACTIVE",
      "lastStateChange": "2026-02-04T21:12:00Z",
      "currentChannel": "1468824206677381210",
      "lastActivity": "2026-02-04T21:14:30Z"
    }
  },
  "channels": {
    "1468824206677381210": {
      "queue": ["pm", "dev"],
      "currentIndex": 1,
      "activeAgent": "dev",
      "turnStartedAt": "2026-02-04T21:12:00Z",
      "turnTimeoutAt": "2026-02-04T21:14:00Z"
    }
  },
  "history": [
    {
      "timestamp": "2026-02-04T21:12:00Z",
      "event": "turn_started",
      "agentId": "dev",
      "channelId": "1468824206677381210",
      "details": {}
    }
  ]
}
```

---

## Read Operations

### loadState()

Load state from file, handle edge cases.

```javascript
async function loadState() {
  const statePath = getStatePath();
  
  try {
    // Try to read existing state
    const content = await fs.readFile(statePath, 'utf8');
    const state = JSON.parse(content);
    
    // Validate version
    if (!state.version) {
      console.warn('State file missing version, migrating...');
      state.version = '1.0';
    }
    
    return state;
    
  } catch (error) {
    if (error.code === 'ENOENT') {
      // File doesn't exist, create default
      console.log('State file not found, creating default...');
      const defaultState = createDefaultState();
      await saveState(defaultState);
      return defaultState;
    }
    
    if (error instanceof SyntaxError) {
      // Corrupted JSON, backup and reset
      console.error('State file corrupted, backing up and resetting...');
      await backupCorruptedState(statePath);
      const defaultState = createDefaultState();
      await saveState(defaultState);
      return defaultState;
    }
    
    throw error;
  }
}

function createDefaultState() {
  return {
    version: '1.0',
    lastUpdated: new Date().toISOString(),
    agents: {},
    channels: {},
    history: []
  };
}
```

### getAgentState(agentId)

Get current state for a specific agent.

```javascript
function getAgentState(state, agentId) {
  return state.agents[agentId] || {
    state: 'OFFLINE',
    lastStateChange: null,
    currentChannel: null,
    lastActivity: null
  };
}
```

### getChannelState(channelId)

Get current state for a specific channel.

```javascript
function getChannelState(state, channelId) {
  return state.channels[channelId] || {
    queue: [],
    currentIndex: 0,
    activeAgent: null,
    turnStartedAt: null,
    turnTimeoutAt: null
  };
}
```

### getActiveAgent(channelId)

Get the currently active agent for a channel.

```javascript
function getActiveAgent(state, channelId) {
  const channel = state.channels[channelId];
  if (!channel) return null;
  return channel.activeAgent;
}
```

---

## Write Operations

### saveState(state)

Atomically write state to file.

```javascript
async function saveState(state) {
  const statePath = getStatePath();
  const tempPath = statePath + '.tmp';
  
  // Update timestamp
  state.lastUpdated = new Date().toISOString();
  
  // Validate before write
  validateState(state);
  
  // Write to temp file
  const content = JSON.stringify(state, null, 2);
  await fs.writeFile(tempPath, content, 'utf8');
  
  // Atomic rename
  await fs.rename(tempPath, statePath);
  
  console.log(`State saved at ${state.lastUpdated}`);
}

function validateState(state) {
  if (!state.version) throw new Error('Missing version');
  if (!state.agents) throw new Error('Missing agents');
  if (!state.channels) throw new Error('Missing channels');
  if (!state.history) throw new Error('Missing history');
}
```

### updateAgentState(agentId, newState, channelId)

Update an agent's state.

```javascript
async function updateAgentState(state, agentId, newAgentState, channelId = null) {
  const now = new Date().toISOString();
  
  // Initialize agent if not exists
  if (!state.agents[agentId]) {
    state.agents[agentId] = {
      state: 'OFFLINE',
      lastStateChange: now,
      currentChannel: null,
      lastActivity: null
    };
  }
  
  // Update state
  const oldState = state.agents[agentId].state;
  state.agents[agentId].state = newAgentState;
  state.agents[agentId].lastStateChange = now;
  
  if (channelId !== null) {
    state.agents[agentId].currentChannel = channelId;
  }
  
  // Add to history
  addToHistory(state, {
    event: 'agent_state_change',
    agentId,
    details: { from: oldState, to: newAgentState }
  });
  
  await saveState(state);
}
```

### updateChannelTurn(channelId, activeAgentId, timeoutSeconds)

Update turn information for a channel.

```javascript
async function updateChannelTurn(state, channelId, activeAgentId, timeoutSeconds) {
  const now = new Date();
  const timeoutAt = new Date(now.getTime() + timeoutSeconds * 1000);
  
  // Initialize channel if not exists
  if (!state.channels[channelId]) {
    state.channels[channelId] = {
      queue: [],
      currentIndex: 0,
      activeAgent: null,
      turnStartedAt: null,
      turnTimeoutAt: null
    };
  }
  
  // Update turn info
  state.channels[channelId].activeAgent = activeAgentId;
  state.channels[channelId].turnStartedAt = now.toISOString();
  state.channels[channelId].turnTimeoutAt = timeoutAt.toISOString();
  
  // Update queue index
  const queue = state.channels[channelId].queue;
  const newIndex = queue.indexOf(activeAgentId);
  if (newIndex !== -1) {
    state.channels[channelId].currentIndex = newIndex;
  }
  
  // Add to history
  addToHistory(state, {
    event: 'turn_started',
    agentId: activeAgentId,
    channelId,
    details: { timeoutAt: timeoutAt.toISOString() }
  });
  
  await saveState(state);
}
```

---

## History Management

### addToHistory(event)

Add event to history ring buffer (max 100 entries).

```javascript
function addToHistory(state, eventData) {
  const MAX_HISTORY = 100;
  
  const entry = {
    timestamp: new Date().toISOString(),
    ...eventData
  };
  
  state.history.push(entry);
  
  // Trim to max size (ring buffer behavior)
  if (state.history.length > MAX_HISTORY) {
    state.history = state.history.slice(-MAX_HISTORY);
  }
}
```

### Event Types

| Event | Description | Details |
|-------|-------------|---------|
| `turn_started` | Agent's turn began | `{ timeoutAt }` |
| `turn_completed` | Agent signaled TURN_COMPLETE | `{ reason, durationSeconds }` |
| `turn_timeout` | Agent's turn timed out | `{ durationSeconds }` |
| `agent_state_change` | Agent state changed | `{ from, to }` |
| `agent_registered` | Agent added to channel | `{ channelId }` |
| `agent_removed` | Agent removed from channel | `{ channelId }` |
| `error` | Error occurred | `{ message, stack }` |

---

## Recovery Procedures

### On Corrupted State File

```javascript
async function backupCorruptedState(statePath) {
  const backupPath = statePath + '.corrupted.' + Date.now();
  try {
    await fs.rename(statePath, backupPath);
    console.log(`Corrupted state backed up to: ${backupPath}`);
  } catch (e) {
    console.error('Failed to backup corrupted state:', e.message);
  }
}
```

### On Startup Recovery

When orchestrator starts, validate state consistency:

```javascript
async function recoverState() {
  const state = await loadState();
  
  // Check for stale active turns
  for (const [channelId, channel] of Object.entries(state.channels)) {
    if (channel.activeAgent && channel.turnTimeoutAt) {
      const timeoutAt = new Date(channel.turnTimeoutAt);
      if (new Date() > timeoutAt) {
        console.log(`Recovering stale turn in channel ${channelId}`);
        // Force advance turn
        await advanceTurn(state, channelId, 'recovery');
      }
    }
  }
  
  // Mark disconnected agents as OFFLINE
  for (const [agentId, agent] of Object.entries(state.agents)) {
    if (agent.state === 'ACTIVE' || agent.state === 'QUEUED') {
      // Check if agent session is actually running
      const isRunning = await checkAgentSession(agentId);
      if (!isRunning) {
        console.log(`Agent ${agentId} not running, marking OFFLINE`);
        await updateAgentState(state, agentId, 'OFFLINE');
      }
    }
  }
  
  return state;
}
```

---

## File Locking (Future)

For multi-process scenarios, implement file locking:

```javascript
// Future: Use proper-lockfile or similar
const lockfile = require('proper-lockfile');

async function withStateLock(operation) {
  const statePath = getStatePath();
  const release = await lockfile.lock(statePath);
  try {
    return await operation();
  } finally {
    await release();
  }
}
```

---

## Configuration

```javascript
const STATE_CONFIG = {
  statePath: '~/.openclaw/workspace/orchestration/state/orchestration-state.json',
  maxHistoryEntries: 100,
  writeDebounceMs: 100,  // Batch rapid writes
  backupOnCorruption: true
};
```

---

## Summary

| Operation | Function | Atomic | Creates File |
|-----------|----------|--------|--------------|
| Load | `loadState()` | N/A | Yes (if missing) |
| Save | `saveState()` | Yes | Yes |
| Update agent | `updateAgentState()` | Yes | No |
| Update turn | `updateChannelTurn()` | Yes | No |
| Add history | `addToHistory()` | Via save | No |
