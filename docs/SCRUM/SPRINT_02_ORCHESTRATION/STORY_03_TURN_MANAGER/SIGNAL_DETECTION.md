# Turn Signal Detection Specification

**Document**: SIGNAL_DETECTION.md  
**Story**: S03 - Build Turn Manager  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document specifies how turn completion signals are detected and how timeouts are handled.

---

## Signal Detection

### The TURN_COMPLETE Signal

Agents signal turn completion by including `TURN_COMPLETE` in their message.

**Valid Formats**:
```
"Here's my response.\n\nTURN_COMPLETE"     ✓
"Done with analysis. TURN_COMPLETE"        ✓
"TURN_COMPLETE"                            ✓
"My answer TURN_COMPLETE more text"        ✗ (not at end)
"turn_complete"                            ✗ (case sensitive)
```

### Detection Regex

```javascript
const TURN_COMPLETE_PATTERN = /TURN_COMPLETE\s*$/;

function hasTurnCompleteSignal(message) {
  return TURN_COMPLETE_PATTERN.test(message);
}
```

### Signal Stripping

Remove the signal before displaying the message to Discord:

```javascript
function stripTurnComplete(message) {
  // Remove TURN_COMPLETE and trailing whitespace
  return message.replace(/\s*TURN_COMPLETE\s*$/, '').trim();
}

// Example:
// Input:  "Here's my analysis.\n\nTURN_COMPLETE"
// Output: "Here's my analysis."
```

### Processing Flow

```javascript
async function processAgentResponse(channelId, agentId, message) {
  // Check if this agent is active
  const state = await loadState();
  const activeAgent = getActiveAgent(state, channelId);
  
  if (agentId !== activeAgent) {
    console.warn(`Message from ${agentId} but ${activeAgent} is active`);
    return { posted: false, reason: 'not_active_agent' };
  }
  
  // Check for turn complete signal
  const hasSignal = hasTurnCompleteSignal(message);
  
  // Strip signal from display message
  const displayMessage = hasSignal 
    ? stripTurnComplete(message) 
    : message;
  
  // Post message to Discord (if content remains)
  if (displayMessage.length > 0) {
    await postToDiscord(channelId, agentId, displayMessage);
  }
  
  // Advance turn if signal detected
  if (hasSignal) {
    const result = await advanceTurn(state, channelId, 'TURN_COMPLETE');
    
    // Notify next agent (optional)
    await notifyAgentTurn(result.nextAgent, channelId);
    
    return { posted: true, turnAdvanced: true, nextAgent: result.nextAgent };
  }
  
  // No signal - update activity timestamp
  state.agents[agentId].lastActivity = new Date().toISOString();
  await saveState(state);
  
  return { posted: true, turnAdvanced: false };
}
```

---

## Timeout Handling

### Timeout Tracking

Each active turn has a deadline:

```javascript
{
  "turnStartedAt": "2026-02-04T21:20:00Z",
  "turnTimeoutAt": "2026-02-04T21:21:30Z"  // 90 seconds later
}
```

### Timeout Checking

Use periodic polling or cron to check for timeouts:

```javascript
async function checkTimeouts() {
  const state = await loadState();
  const now = new Date();
  
  for (const [channelId, channel] of Object.entries(state.channels)) {
    if (!channel.turnTimeoutAt || !channel.activeAgent) continue;
    
    const timeoutAt = new Date(channel.turnTimeoutAt);
    
    if (now > timeoutAt) {
      console.log(`Timeout detected: ${channel.activeAgent} in ${channelId}`);
      await handleTimeout(state, channelId, channel.activeAgent);
    }
  }
}

// Run every 10 seconds
setInterval(checkTimeouts, 10000);
```

### Timeout Handling

```javascript
async function handleTimeout(state, channelId, agentId) {
  // Calculate how long the turn lasted
  const channel = state.channels[channelId];
  const turnStarted = new Date(channel.turnStartedAt);
  const duration = Math.round((Date.now() - turnStarted.getTime()) / 1000);
  
  // Post timeout notice (if configured)
  const config = getConfig();
  if (config.announceTimeouts) {
    await postToDiscord(channelId, 'system', 
      `⏱️ ${agentId} turn timed out after ${duration}s`
    );
  }
  
  // Advance turn
  const result = await advanceTurn(state, channelId, 'TIMEOUT');
  
  // Log the timeout
  addToHistory(state, {
    event: 'turn_timeout',
    agentId,
    channelId,
    details: { durationSeconds: duration, nextAgent: result.nextAgent }
  });
  
  await saveState(state);
  
  // Notify next agent
  await notifyAgentTurn(result.nextAgent, channelId);
}
```

---

## Configuration

### Signal Configuration

```javascript
const SIGNAL_CONFIG = {
  // Primary completion signal
  turnCompleteSignal: 'TURN_COMPLETE',
  
  // Case sensitive matching
  caseSensitive: true,
  
  // Require signal at end of message
  requireAtEnd: true,
  
  // Alternative signals (future)
  alternativeSignals: ['DONE', 'PASS', 'NEXT']
};
```

### Timeout Configuration

```javascript
const TIMEOUT_CONFIG = {
  // Default timeout if not specified in persona
  defaultTimeoutSeconds: 60,
  
  // Check interval for timeout polling
  checkIntervalMs: 10000,
  
  // Post timeout notices to channel
  announceTimeouts: true,
  
  // Timeout announcement template
  timeoutMessage: '⏱️ {agent} turn timed out after {duration}s',
  
  // Grace period after timeout before forcing (future)
  graceSeconds: 5
};
```

### Per-Agent Timeout

From persona config:

```javascript
{
  "turnBehavior": {
    "timeoutSeconds": 90  // PM gets 90 seconds
  }
}
```

---

## Edge Cases

### Agent Sends Multiple Messages

```javascript
// Each message extends activity, but only TURN_COMPLETE ends turn
async function handleMultipleMessages(messages) {
  for (const msg of messages) {
    const result = await processAgentResponse(channelId, agentId, msg);
    if (result.turnAdvanced) {
      // Turn ended, ignore remaining messages
      break;
    }
  }
}
```

### Empty Message After Signal Stripped

```javascript
// If message is ONLY "TURN_COMPLETE", nothing to post
const displayMessage = stripTurnComplete("TURN_COMPLETE");
// displayMessage = ""

if (displayMessage.length > 0) {
  await postToDiscord(...);
}
// Turn still advances even with no displayed message
```

### Signal in Wrong Position

```javascript
// Signal must be at END
"TURN_COMPLETE and here's more"  // NOT detected
"Here's my response TURN_COMPLETE" // Detected
```

### Late Signal After Timeout

```javascript
async function processLateSignal(channelId, agentId, message) {
  const state = await loadState();
  const channel = state.channels[channelId];
  
  // Check if this agent is still active (might have timed out)
  if (channel.activeAgent !== agentId) {
    console.log(`Late signal from ${agentId}, no longer active`);
    // Still post the message, but don't advance turn
    const displayMessage = stripTurnComplete(message);
    await postToDiscord(channelId, agentId, displayMessage);
    return { posted: true, turnAdvanced: false, reason: 'late_signal' };
  }
  
  // Normal processing
  return processAgentResponse(channelId, agentId, message);
}
```

### No Signal, No Timeout (Agent Active Indefinitely)

```javascript
// This is okay - agent stays active until:
// 1. They send TURN_COMPLETE
// 2. Timeout occurs
// 3. They're manually removed
// 4. They disconnect

// To prevent indefinitely stuck agents:
// - Always configure reasonable timeouts
// - Monitor for "stuck" turns in heartbeat checks
```

---

## Auto-Detection (Future Enhancement)

Detect "natural" turn completion without explicit signal:

```javascript
const AUTO_DETECT_CONFIG = {
  enabled: false,
  
  // End turn after N seconds of silence
  silenceThresholdSeconds: 30,
  
  // End turn if message seems complete (ends with period, etc.)
  detectPunctuation: true,
  
  // End turn if agent says "done", "finished", etc.
  completionPhrases: ['done', 'finished', 'complete', 'that\'s all']
};

function detectNaturalCompletion(message) {
  if (!AUTO_DETECT_CONFIG.enabled) return false;
  
  // Check for completion phrases
  const lowerMessage = message.toLowerCase();
  for (const phrase of AUTO_DETECT_CONFIG.completionPhrases) {
    if (lowerMessage.includes(phrase)) {
      return true;
    }
  }
  
  return false;
}
```

---

## Integration with OpenClaw

### Message Event Hook

```javascript
// In OpenClaw message handler
openclaw.on('agentResponse', async (event) => {
  const { channelId, agentId, message } = event;
  
  // Check if orchestration is active for this channel
  const state = await loadState();
  if (!state.channels[channelId]) {
    // Not an orchestrated channel, ignore
    return;
  }
  
  // Process through turn manager
  await processAgentResponse(channelId, agentId, message);
});
```

### Cron-Based Timeout Check

```javascript
// In HEARTBEAT.md or cron job
// Check for timed-out turns every 30 seconds
await checkTimeouts();
```

---

## Summary

| Detection | Method | Configurable |
|-----------|--------|--------------|
| Turn Complete | Regex at end of message | Signal keyword |
| Timeout | Periodic polling | Timeout per agent |
| Auto-detect | Future enhancement | Silence threshold |

| Configuration | Default | Per-Agent |
|---------------|---------|-----------|
| Signal keyword | TURN_COMPLETE | No |
| Timeout | 60 seconds | Yes (persona) |
| Announce timeouts | Yes | No |
| Check interval | 10 seconds | No |
