# Task: Test MS Paint Command

**Story**: 03 - Validate Acceptance Criteria  
**Task ID**: STORY_03_TASK_02  
**Status**: Not Started

## Objective
Execute the sprint acceptance test - open MS Paint via Discord message.

## Steps

### 1. Open Discord
Navigate to the channel or DM where OpenClaw bot is available.

### 2. Send Command
Send a message like:
- "Open MS Paint"
- "Launch mspaint on this computer"
- "Can you open the Paint application?"

### 3. Observe Results
Within seconds, MS Paint should launch on the Windows desktop.

### 4. Verify Execution
- [ ] Message sent to Discord
- [ ] OpenClaw acknowledges the request
- [ ] MS Paint window appears on screen
- [ ] OpenClaw confirms action completed

## Expected Outcome

**SPRINT COMPLETE** when:
> Discord message → OpenClaw → MS Paint opens

## Technical Background
Windows command to open MS Paint:
```powershell
Start-Process mspaint
# or simply
mspaint
```

OpenClaw should translate the natural language request into this command.

## Troubleshooting
- Paint doesn't open: Check shell access is enabled (Task 01)
- Permission denied: Windows may block - check UAC/defender
- Wrong app opens: Clarify request to OpenClaw

## Celebration
When this works, Sprint 1 is DONE! 🎉
The acceptance criteria has been met.
