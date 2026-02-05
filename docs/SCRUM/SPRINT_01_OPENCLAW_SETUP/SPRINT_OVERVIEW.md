# Sprint 1: OpenClaw Setup

## Sprint Goal
Get OpenClaw running on this Windows machine with Discord integration, capable of executing system commands.

## Acceptance Criteria
**Definition of Done**: Send a message to OpenClaw on Discord and have it open MS Paint on this machine autonomously.

**STATUS: COMPLETE** ✅ (2026-02-04)

## Sprint Duration
Target: 1 session
Actual: 1 session

## Stories

| ID | Story | Points | Status |
|----|-------|--------|--------|
| S01 | Install OpenClaw | 3 | Done |
| S02 | Configure Discord Integration | 2 | Done |
| S03 | Validate Acceptance Criteria | 1 | Done |

**Total Points**: 6 (all completed)

## Technical Context

### What is OpenClaw?
OpenClaw is an open-source personal AI assistant that:
- Runs locally on your machine (Windows/Mac/Linux)
- Connects to chat apps (Discord, Telegram, WhatsApp, etc.)
- Has full system access (shell commands, browser control, file access)
- Has persistent memory across sessions
- Can be extended with custom "skills"

### Key Resources
- Website: https://openclaw.ai/
- Docs: https://docs.openclaw.ai/getting-started
- GitHub: https://github.com/openclaw/openclaw
- Discord Community: https://discord.com/invite/clawd

### Windows Installation
PowerShell one-liner:
```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

## Notes
- Created by Peter Steinberger (@steipete)
- Formerly known as Clawdbot and Moltbot
- Independent project, not affiliated with Anthropic
