# Task: Test Bot Connection

**Story**: 02 - Configure Discord Integration  
**Task ID**: STORY_02_TASK_03  
**Status**: Not Started

## Objective
Verify OpenClaw responds to Discord messages.

## Steps

### 1. Send Test Message
In Discord, send a message to the bot:
- Direct message to bot, OR
- @mention in a channel where bot exists

Example messages:
- "Hello"
- "What's your name?"
- "What can you do?"

### 2. Verify Response
Bot should respond conversationally, demonstrating:
- Message received successfully
- AI processing works
- Bot can send responses back

### 3. Test Multiple Interactions
Have a brief conversation to confirm:
- Memory/context works between messages
- Response time is acceptable
- No errors or disconnections

## Expected Outcome
- Bot responds to messages in Discord
- Two-way communication confirmed
- Ready for system access testing (Story 03)

## Success Criteria
- [ ] Bot receives messages
- [ ] Bot sends responses
- [ ] Responses are coherent (AI working)
- [ ] No connection drops during testing

## Troubleshooting
- No response: Check OpenClaw logs, verify bot token
- Garbled response: Check AI provider API key
- Delayed response: Normal for first message, check if consistent
