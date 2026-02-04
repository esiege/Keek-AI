# Task: Create Discord Bot

**Story**: 02 - Configure Discord Integration  
**Task ID**: STORY_02_TASK_01  
**Status**: Not Started

## Objective
Create a Discord bot application for OpenClaw to use.

## Steps

### 1. Go to Discord Developer Portal
https://discord.com/developers/applications

### 2. Create New Application
- Click "New Application"
- Name it (e.g., "Keek OpenClaw" or custom name)
- Accept Terms of Service

### 3. Configure Bot
- Go to "Bot" section in left sidebar
- Click "Add Bot" if needed
- Configure bot settings:
  - Username (visible name in Discord)
  - Icon (optional)

### 4. Get Bot Token
- Under "Token" section, click "Reset Token" or "Copy"
- **SAVE THIS TOKEN SECURELY** - you won't see it again
- Never commit token to git

### 5. Configure Intents
Enable these Gateway Intents:
- [x] MESSAGE CONTENT INTENT (required for reading messages)
- [x] SERVER MEMBERS INTENT (if needed)

### 6. Generate Invite URL
- Go to "OAuth2" → "URL Generator"
- Select scopes: `bot`
- Select permissions: 
  - Send Messages
  - Read Message History
  - View Channels
- Copy the generated URL

### 7. Add Bot to Server
- Open the invite URL in browser
- Select your Discord server
- Authorize

## Expected Outcome
- Bot appears in Discord server (offline until OpenClaw connects)
- Bot token saved securely for next task

## Security Notes
- Store token in secrets/ directory or environment variable
- Add token file to .gitignore
