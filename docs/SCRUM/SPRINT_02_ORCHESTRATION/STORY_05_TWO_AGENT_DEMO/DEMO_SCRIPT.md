# Two-Agent Demo Script

**Document**: DEMO_SCRIPT.md  
**Story**: S05 - Two-Agent Demo  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Demo Overview

**Goal**: Validate the orchestration system by having PM and Dev agents take turns on a simple task.

**Scenario**: "Spec and implement a /hello command"

**Success Criteria**: 
- PM responds first (specs the feature)
- PM signals TURN_COMPLETE
- Dev responds second (implements)
- Dev signals TURN_COMPLETE
- Turn order is respected throughout

---

## Prerequisites

Before running the demo, ensure:

- [ ] OpenClaw running
- [ ] Discord bot connected
- [ ] Persona files exist (`pm.json`, `dev.json`)
- [ ] Channel configuration ready
- [ ] State file initialized
- [ ] Test channel created (e.g., #reviews)

---

## Setup Steps

### 1. Create Test Channel

Create a Discord channel for the demo:
- Name: `#demo-reviews`
- Add Keek AI bot
- Note channel ID

### 2. Configure Channel

Add to `channels.json`:
```json
"<demo_channel_id>": {
  "name": "demo-reviews",
  "mode": "turn-based",
  "agents": ["pm", "dev"],
  "humanInterruptMode": "inject"
}
```

### 3. Initialize Turn Queue

Initialize the queue with PM first:
```javascript
// PM and Dev assigned, PM starts first
queue: ["pm", "dev"]
currentIndex: 0
activeAgent: "pm"
```

### 4. Clear Previous State

Reset state file to clean starting point.

---

## Demo Execution

### Step 1: Human Initiates Task

**Post in #demo-reviews:**
```
Let's build a /hello command.

PM: Please spec this feature.
Dev: Implement after PM is done.

This is a turn-based channel. PM goes first, then Dev.
```

**Expected**: Message delivered to PM (active agent)

---

### Step 2: PM Responds

**PM should:**
1. Receive the message
2. Write a brief spec
3. End with TURN_COMPLETE

**Expected PM Response:**
```
## /hello Command Specification

**User Story**: As a user, I want to type /hello and receive a 
friendly greeting so I feel welcomed.

**Acceptance Criteria**:
- Command: `/hello`
- Response includes user's display name
- Tone is friendly and welcoming
- Response time < 2 seconds

**Example**:
- User types: `/hello`
- Bot responds: "Hey @username! 👋 Welcome!"

Dev, please implement this.

TURN_COMPLETE
```

**Verify:**
- [ ] PM responded (not Dev)
- [ ] Response is PM-like (specs, not code)
- [ ] TURN_COMPLETE detected
- [ ] Turn advanced to Dev

---

### Step 3: Dev Responds

**Dev should:**
1. See the spec from PM
2. Describe or implement the feature
3. End with TURN_COMPLETE

**Expected Dev Response:**
```
Got the spec! Here's my implementation approach:

```javascript
// /hello command handler
bot.on('interactionCreate', async (interaction) => {
  if (interaction.commandName === 'hello') {
    const name = interaction.user.displayName;
    await interaction.reply(`Hey ${name}! 👋 Welcome!`);
  }
});
```

I've implemented:
- Slash command handler for /hello
- Gets user's display name
- Sends friendly greeting with emoji

Ready for review!

TURN_COMPLETE
```

**Verify:**
- [ ] Dev responded (not PM)
- [ ] Response is Dev-like (code, technical)
- [ ] TURN_COMPLETE detected
- [ ] Turn advanced to PM

---

### Step 4: PM Reviews (Optional)

**PM should:**
1. Review Dev's implementation
2. Approve or request changes

**Expected PM Response:**
```
Looks good! The implementation meets all acceptance criteria:

✅ Command is /hello
✅ Includes user's display name
✅ Friendly tone with emoji
✅ Simple enough to be fast

Approved! Great work.

TURN_COMPLETE
```

---

## Verification Checklist

### Turn Order
- [ ] PM responded first
- [ ] Dev responded second
- [ ] PM responded third (if continued)
- [ ] No agent spoke out of turn

### Signal Detection
- [ ] TURN_COMPLETE stripped from displayed messages
- [ ] Turn advanced after each TURN_COMPLETE
- [ ] No premature turn advancement

### State Persistence
- [ ] State file updated after each turn
- [ ] History shows turn events
- [ ] Queue index correct throughout

### Agent Behavior
- [ ] PM focused on specs (not code)
- [ ] Dev focused on implementation
- [ ] Both followed turn protocol

---

## Troubleshooting

### PM Not Responding
1. Check PM is in channel's agent list
2. Check PM is ACTIVE in state file
3. Check PM session is running

### Dev Responding Out of Turn
1. Check turn queue order
2. Check currentIndex in state
3. Verify Dev is QUEUED, not ACTIVE

### TURN_COMPLETE Not Detected
1. Check spelling (case-sensitive)
2. Check it's at end of message
3. Check signal detection regex

### Turn Not Advancing
1. Check signal was detected
2. Check advanceTurn() was called
3. Check state file was saved

---

## Sign-Off Section

### Demo Date
_________________

### Participants
- [ ] Jesse (Product Owner)
- [ ] Keek (Orchestrator)

### Demo Result
- [ ] **PASS** — All criteria met
- [ ] **PARTIAL** — Some issues (list below)
- [ ] **FAIL** — Significant issues

### Issues Found
1. _______________
2. _______________
3. _______________

### Notes
_________________
_________________
_________________

### Jesse's Feedback
> _________________
> _________________

### Sign-Off
- [ ] **APPROVED** — Sprint 02 complete
- [ ] **NEEDS WORK** — Issues must be resolved

**Signed**: _________________ **Date**: _________________

---

## Post-Demo Actions

### If PASS
1. Commit final state
2. Update sprint status to COMPLETE
3. Plan Sprint 03 (or next steps)
4. Celebrate! 🎉

### If NEEDS WORK
1. Document issues in sprint notes
2. Create bug/fix tasks
3. Re-run demo after fixes
4. Get new sign-off

---

## Summary

This demo validates the core orchestration system:

| Component | Validated By |
|-----------|--------------|
| Turn Queue | PM → Dev → PM order |
| Signal Detection | TURN_COMPLETE advances turn |
| State Management | Persisted between turns |
| Channel Routing | Messages go to active agent |
| Personas | PM specs, Dev codes |

**Sprint 02 is complete when this demo passes.**
