# Task: Configure OpenClaw Discord Integration

**Story**: 02 - Configure Discord Integration  
**Task ID**: STORY_02_TASK_02  
**Status**: Not Started

## Objective
Connect the Discord bot to OpenClaw configuration.

## Steps

### 1. Add Discord Integration to OpenClaw
Check OpenClaw configuration file location:
```powershell
# Typically at:
# %USERPROFILE%\.openclaw\config.yaml
# or similar location
```

### 2. Configure Discord Token
Add the bot token from previous task. This might be done via:
- CLI command: `claw config set discord.token <token>`
- Config file edit
- Environment variable: `DISCORD_BOT_TOKEN`

### 3. Enable Discord Integration
```powershell
claw integrations enable discord
# or equivalent command
```

### 4. Start OpenClaw with Discord
```powershell
claw start
```

### 5. Verify Bot Goes Online
Check Discord - bot status should change from offline to online.

## Expected Outcome
- OpenClaw connects to Discord
- Bot shows as "online" in Discord server
- Ready for message testing

## Notes
- Consult OpenClaw docs for exact config syntax
- May need to restart OpenClaw after config changes
- Check logs if connection fails: `claw logs` or similar

## Troubleshooting
- Wrong token: Bot won't connect, check copy/paste
- Missing intents: Bot connects but can't read messages
- Firewall: Ensure outbound connections allowed
