# Task: Enable Shell/System Access

**Story**: 03 - Validate Acceptance Criteria  
**Task ID**: STORY_03_TASK_01  
**Status**: Not Started

## Objective
Configure OpenClaw to execute shell commands on this Windows machine.

## Steps

### 1. Check Current Permissions
OpenClaw may have system access disabled by default for safety.

### 2. Enable Bash/Shell Integration
Per OpenClaw docs, shell access might be configured via:
```powershell
claw integrations enable bash
# or
claw integrations enable shell
```

Or in config file - enable the bash/computer tool.

### 3. Configure Windows Shell
Ensure it's configured for Windows (PowerShell or cmd, not bash):
- May need to set shell type to `powershell` or `cmd`

### 4. Test Shell Access
Ask OpenClaw (via Discord or local) to run a simple command:
- "What's the current directory?"
- "List files in my Documents folder"

### 5. Verify Command Execution
Check that:
- OpenClaw can execute commands
- Output is returned correctly
- No permission errors

## Expected Outcome
- OpenClaw can run shell commands on Windows
- Ready for the MS Paint test

## Security Notes
- Understand what level of access you're granting
- OpenClaw can be configured with sandboxing
- Review logs to see what commands are executed

## Reference
- OpenClaw Bash Docs: https://docs.openclaw.ai/bash
