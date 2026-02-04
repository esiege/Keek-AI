# Task: Configure Initial Persona

**Story**: 01 - Install OpenClaw  
**Task ID**: STORY_01_TASK_03  
**Status**: Not Started

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
