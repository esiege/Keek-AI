# Turn Queue Logic Specification

**Document**: TURN_QUEUE.md  
**Story**: S03 - Build Turn Manager  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document specifies the turn queue algorithm for round-robin turn management.

---

## Queue Data Structure

Each channel maintains its own queue:

```javascript
{
  "queue": ["pm", "dev", "qa"],  // Agent IDs in turn order
  "currentIndex": 0,             // Current position (0 = pm's turn)
  "activeAgent": "pm"            // Currently active agent
}
```

---

## Core Operations

### 1. initializeQueue(channelId, agentIds)

Set up a new queue for a channel.

```javascript
async function initializeQueue(state, channelId, agentIds) {
  // Validate agents exist
  for (const agentId of agentIds) {
    if (!state.agents[agentId]) {
      console.warn(`Agent ${agentId} not registered, skipping`);
      agentIds = agentIds.filter(id => id !== agentId);
    }
  }
  
  if (agentIds.length === 0) {
    throw new Error(`Cannot initialize queue with no valid agents`);
  }
  
  // Create channel state
  state.channels[channelId] = {
    queue: agentIds,
    currentIndex: 0,
    activeAgent: agentIds[0],
    turnStartedAt: new Date().toISOString(),
    turnTimeoutAt: calculateTimeout(state, agentIds[0])
  };
  
  // Set first agent to ACTIVE, others to QUEUED
  for (let i = 0; i < agentIds.length; i++) {
    const newState = i === 0 ? 'ACTIVE' : 'QUEUED';
    state.agents[agentIds[i]].state = newState;
    state.agents[agentIds[i]].currentChannel = channelId;
    state.agents[agentIds[i]].lastStateChange = new Date().toISOString();
  }
  
  addToHistory(state, {
    event: 'queue_initialized',
    channelId,
    details: { agents: agentIds, firstActive: agentIds[0] }
  });
  
  await saveState(state);
  return state.channels[channelId];
}
```

---

### 2. advanceTurn(channelId, reason)

Move to the next agent in the queue.

```javascript
async function advanceTurn(state, channelId, reason = 'TURN_COMPLETE') {
  const channel = state.channels[channelId];
  if (!channel) {
    throw new Error(`Channel ${channelId} not found`);
  }
  
  if (channel.queue.length === 0) {
    throw new Error(`Channel ${channelId} has empty queue`);
  }
  
  // Get previous agent
  const previousAgent = channel.activeAgent;
  const previousIndex = channel.currentIndex;
  
  // Calculate turn duration
  const turnStarted = new Date(channel.turnStartedAt);
  const turnDuration = Math.round((Date.now() - turnStarted.getTime()) / 1000);
  
  // Calculate next index (wrap around)
  const nextIndex = (previousIndex + 1) % channel.queue.length;
  const nextAgent = channel.queue[nextIndex];
  
  // Update channel state
  channel.currentIndex = nextIndex;
  channel.activeAgent = nextAgent;
  channel.turnStartedAt = new Date().toISOString();
  channel.turnTimeoutAt = calculateTimeout(state, nextAgent);
  
  // Update agent states
  if (previousAgent) {
    state.agents[previousAgent].state = 'QUEUED';
    state.agents[previousAgent].lastStateChange = new Date().toISOString();
  }
  
  state.agents[nextAgent].state = 'ACTIVE';
  state.agents[nextAgent].lastStateChange = new Date().toISOString();
  state.agents[nextAgent].lastActivity = new Date().toISOString();
  
  // Add to history
  addToHistory(state, {
    event: 'turn_completed',
    agentId: previousAgent,
    channelId,
    details: { reason, durationSeconds: turnDuration }
  });
  
  addToHistory(state, {
    event: 'turn_started',
    agentId: nextAgent,
    channelId,
    details: { previousAgent, turnNumber: nextIndex + 1 }
  });
  
  await saveState(state);
  
  return {
    previousAgent,
    nextAgent,
    turnDuration,
    reason
  };
}
```

---

### 3. addAgentToQueue(channelId, agentId, position)

Add an agent to a channel's queue.

```javascript
async function addAgentToQueue(state, channelId, agentId, position = 'end') {
  // Validate agent exists
  if (!state.agents[agentId]) {
    throw new Error(`Agent ${agentId} not found`);
  }
  
  // Get or create channel
  if (!state.channels[channelId]) {
    state.channels[channelId] = {
      queue: [],
      currentIndex: 0,
      activeAgent: null,
      turnStartedAt: null,
      turnTimeoutAt: null
    };
  }
  
  const channel = state.channels[channelId];
  
  // Check if already in queue
  if (channel.queue.includes(agentId)) {
    console.warn(`Agent ${agentId} already in queue for ${channelId}`);
    return;
  }
  
  // Add to queue
  if (position === 'end') {
    channel.queue.push(agentId);
  } else if (position === 'start') {
    channel.queue.unshift(agentId);
    // Adjust current index if needed
    if (channel.currentIndex >= 0) {
      channel.currentIndex++;
    }
  } else if (typeof position === 'number') {
    channel.queue.splice(position, 0, agentId);
    if (position <= channel.currentIndex) {
      channel.currentIndex++;
    }
  }
  
  // Update agent state
  state.agents[agentId].state = 'QUEUED';
  state.agents[agentId].currentChannel = channelId;
  state.agents[agentId].lastStateChange = new Date().toISOString();
  
  // If this is the first/only agent, make them active
  if (channel.queue.length === 1) {
    channel.activeAgent = agentId;
    channel.currentIndex = 0;
    channel.turnStartedAt = new Date().toISOString();
    channel.turnTimeoutAt = calculateTimeout(state, agentId);
    state.agents[agentId].state = 'ACTIVE';
  }
  
  addToHistory(state, {
    event: 'agent_registered',
    agentId,
    channelId,
    details: { position, queueLength: channel.queue.length }
  });
  
  await saveState(state);
}
```

---

### 4. removeAgentFromQueue(channelId, agentId)

Remove an agent from a channel's queue.

```javascript
async function removeAgentFromQueue(state, channelId, agentId) {
  const channel = state.channels[channelId];
  if (!channel) {
    throw new Error(`Channel ${channelId} not found`);
  }
  
  const agentIndex = channel.queue.indexOf(agentId);
  if (agentIndex === -1) {
    console.warn(`Agent ${agentId} not in queue for ${channelId}`);
    return;
  }
  
  const wasActive = channel.activeAgent === agentId;
  
  // Remove from queue
  channel.queue.splice(agentIndex, 1);
  
  // Update agent state
  state.agents[agentId].state = 'IDLE';
  state.agents[agentId].currentChannel = null;
  state.agents[agentId].lastStateChange = new Date().toISOString();
  
  // Handle removal of active agent
  if (wasActive) {
    if (channel.queue.length > 0) {
      // Advance to next agent
      channel.currentIndex = channel.currentIndex % channel.queue.length;
      channel.activeAgent = channel.queue[channel.currentIndex];
      channel.turnStartedAt = new Date().toISOString();
      channel.turnTimeoutAt = calculateTimeout(state, channel.activeAgent);
      
      state.agents[channel.activeAgent].state = 'ACTIVE';
      state.agents[channel.activeAgent].lastStateChange = new Date().toISOString();
      
      addToHistory(state, {
        event: 'turn_started',
        agentId: channel.activeAgent,
        channelId,
        details: { reason: 'previous_agent_removed' }
      });
    } else {
      // Queue is now empty
      channel.activeAgent = null;
      channel.currentIndex = 0;
      channel.turnStartedAt = null;
      channel.turnTimeoutAt = null;
    }
  } else {
    // Adjust index if removed agent was before current
    if (agentIndex < channel.currentIndex) {
      channel.currentIndex--;
    }
  }
  
  addToHistory(state, {
    event: 'agent_removed',
    agentId,
    channelId,
    details: { wasActive, remainingAgents: channel.queue.length }
  });
  
  await saveState(state);
}
```

---

## Helper Functions

### calculateTimeout(agentId)

Get timeout for an agent based on their persona config.

```javascript
function calculateTimeout(state, agentId) {
  // Load persona config
  const persona = loadPersona(agentId);
  const timeoutSeconds = persona?.turnBehavior?.timeoutSeconds || 60;
  
  const timeoutAt = new Date(Date.now() + timeoutSeconds * 1000);
  return timeoutAt.toISOString();
}
```

### getQueuePosition(channelId, agentId)

Get an agent's position in the queue.

```javascript
function getQueuePosition(state, channelId, agentId) {
  const channel = state.channels[channelId];
  if (!channel) return -1;
  return channel.queue.indexOf(agentId);
}
```

### getTurnsUntil(channelId, agentId)

Get number of turns until an agent's turn.

```javascript
function getTurnsUntil(state, channelId, agentId) {
  const channel = state.channels[channelId];
  if (!channel) return -1;
  
  const agentIndex = channel.queue.indexOf(agentId);
  if (agentIndex === -1) return -1;
  
  if (agentIndex >= channel.currentIndex) {
    return agentIndex - channel.currentIndex;
  } else {
    return (channel.queue.length - channel.currentIndex) + agentIndex;
  }
}
```

---

## Edge Cases

### Empty Queue

```javascript
// When last agent removed
if (channel.queue.length === 0) {
  channel.activeAgent = null;
  channel.currentIndex = 0;
  // Channel remains but is inactive
  // Messages to channel will be logged but not routed
}
```

### Single Agent

```javascript
// Single agent always stays active
// advanceTurn() wraps to same agent
if (channel.queue.length === 1) {
  // Still log turn completion for consistency
  // Reset timeout
  channel.turnStartedAt = new Date().toISOString();
  channel.turnTimeoutAt = calculateTimeout(state, agentId);
}
```

### Agent Added While Turn Active

```javascript
// New agent goes to end of queue
// Current turn continues uninterrupted
// New agent will get turn in next cycle
```

### Active Agent Removed

```javascript
// Immediately advance to next agent
// Log the interruption
// If last agent, queue becomes empty
```

---

## Round-Robin Visualization

```
Queue: [PM, Dev, QA]
Index: 0  1   2

Turn 1: PM active   (index=0)
         ↓
Turn 2: Dev active  (index=1)
         ↓
Turn 3: QA active   (index=2)
         ↓
Turn 4: PM active   (index=0) ← wraps around
         ↓
        ...
```

---

## Summary

| Operation | Complexity | Notes |
|-----------|------------|-------|
| initializeQueue | O(n) | n = number of agents |
| advanceTurn | O(1) | Index increment + wrap |
| addAgentToQueue | O(n) | Insert + possible shift |
| removeAgentFromQueue | O(n) | Find + splice |
| getQueuePosition | O(n) | Linear search |
| getTurnsUntil | O(1) | Index math |
