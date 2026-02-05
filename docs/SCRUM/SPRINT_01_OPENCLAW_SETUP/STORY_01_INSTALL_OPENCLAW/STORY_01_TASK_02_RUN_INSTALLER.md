# Task: Run OpenClaw Installer

**Story**: 01 - Install OpenClaw  
**Task ID**: STORY_01_TASK_02  
**Status**: Done

## Results (2026-02-04)

| Component | Status |
|-----------|--------|
| Node.js | v24.13.0 installed |
| OpenClaw | v2026.2.2-3 (9c5941b) |
| Workspace | `~\.openclaw\workspace` |
| Config | `~\.openclaw\openclaw.json` |

## Objective
Install OpenClaw using the official Windows installer.

## Steps

### 1. Run Official Installer
```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

### 2. Follow Setup Wizard
The installer will:
- Install Node.js if needed
- Download OpenClaw
- Create initial configuration
- Ask for AI provider API key

### 3. Verify Installation
```powershell
claw --version
# or
openclaw --version
```

## Expected Outcome
- OpenClaw CLI installed and accessible
- Configuration file created
- Ready to configure integrations

## Notes
- Will need an Anthropic or OpenAI API key
- Keep API key secure - do not commit to git
- Installation location typically in user profile

## Troubleshooting
- If installer fails, try "Hackable" install from docs
- Check Windows Defender isn't blocking scripts
