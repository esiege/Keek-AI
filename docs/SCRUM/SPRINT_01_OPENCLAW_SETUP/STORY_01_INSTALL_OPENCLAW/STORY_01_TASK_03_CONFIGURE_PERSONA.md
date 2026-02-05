# Task: Configure Initial Persona

**Story**: 01 - Install OpenClaw  
**Task ID**: STORY_01_TASK_03  
**Status**: Done

## Results (2026-02-04)

### Gateway Configuration
- **Gateway Target**: `ws://127.0.0.1:18789`
- **Source**: local loopback
- **Config**: `~\.openclaw\openclaw.json`
- **Bind**: loopback

### Control UI
- **Web UI**: http://127.0.0.1:18789/
- **Gateway WS**: ws://127.0.0.1:18789
- **Token**: Stored locally (do not commit)

### Optional Features (Not Configured)
- Web search requires Brave Search API key
- Mobile apps available (iOS/Android) for camera/canvas
- macOS app for system notifications

### Helpful Commands
```powershell
openclaw configure --section web    # Add web search
openclaw security audit --deep      # Security check
openclaw dashboard                  # Open Control UI
```

### Documentation Links
- Health: https://docs.openclaw.ai/gateway/health
- Troubleshooting: https://docs.openclaw.ai/gateway/troubleshooting
- Control UI: https://docs.openclaw.ai/web/control-ui
- Security: https://docs.openclaw.ai/security
- Workspace: https://docs.openclaw.ai/concepts/agent-workspace

## Objective
Complete OpenClaw persona setup and verify basic functionality.

## Steps

### 1. Initial Configuration
The setup wizard will ask about:
- AI provider (Anthropic Claude / OpenAI GPT)
- API key
- Persona name
- Basic preferences

### 2. Test Local Functionality
```powershell
# Start OpenClaw
claw start

# Or run in foreground to see output
claw run
```

### 3. Verify AI Response
Test that the AI can respond to basic prompts before adding integrations.

## Expected Outcome
- OpenClaw starts without errors
- AI can process and respond to inputs
- Ready for Discord integration

## Notes
- Persona can be customized later
- Keek AI studio context could be added to persona
- Memory and skills can be configured after basic setup works

## Configuration Location
Typically: `~/.openclaw/` or `%USERPROFILE%\.openclaw\`
