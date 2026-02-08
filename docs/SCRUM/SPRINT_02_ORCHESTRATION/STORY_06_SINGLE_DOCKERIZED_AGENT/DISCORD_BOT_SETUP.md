# Discord Bot Setup Guide

This guide documents how to create a new Discord bot application for agent personas.

## Prerequisites
- Discord account with access to Developer Portal
- Server with "Manage Server" permission (to invite bot)

## Step-by-Step Process

### 1. Create Discord Application

1. Navigate to https://discord.com/developers/applications
2. Click **"New Application"**
3. Enter application name (e.g., "PM Agent")
4. Check the agreement checkbox
5. Click **"Create"**

*Note: You may encounter a captcha ("I am human" checkbox)*

**Record the following:**
- Application ID (visible on General Information page)

### 2. Create Bot User

1. In the left sidebar, click **"Bot"**
2. The bot user is auto-created with the application
3. Note the username (e.g., "PM Agent#3743")

### 3. Get Bot Token

1. On the Bot page, click **"Reset Token"**
2. Confirm with your Discord password (MFA required)
3. **COPY THE TOKEN IMMEDIATELY** — it's only shown once
4. Store securely (e.g., Credential Manager, .env file)

**Security Note:** Never commit tokens to git. Use environment variables or secret managers.

### 4. Enable Required Intents

On the Bot page, scroll to **"Privileged Gateway Intents"**:

- [ ] Presence Intent — optional, for user presence info
- [ ] Server Members Intent — optional, for member events  
- [x] **Message Content Intent** — REQUIRED for reading messages

Click **"Save Changes"** after enabling.

### 5. Generate Invite URL

Format: `https://discord.com/oauth2/authorize?client_id=<APP_ID>&permissions=8&scope=bot`

Replace `<APP_ID>` with your Application ID.

**Permission 8 = Administrator** (simplest for dev, restrict for production)

### 6. Invite Bot to Server

1. Open the invite URL in browser
2. Select target server from dropdown
3. Click **"Continue"**
4. Confirm Administrator permission
5. Click **"Authorize"**
6. Complete captcha if prompted

Bot should now appear in server member list.

---

## Example: PM Agent (Pearl)

**Created:** 2026-02-05 | **Renamed:** 2026-02-05

| Field | Value |
|-------|-------|
| Application ID | 1469026037110407340 |
| Bot Username | [PM] - Pearl#3743 |
| Token Storage | Credential Manager: `PM_Agent_Discord` |
| Invite URL | https://discord.com/oauth2/authorize?client_id=1469026037110407340&permissions=8&scope=bot |
| Server | Keek Dev Studio |
| Intents | Message Content ✅ |

---

## Example: Dev Agent (Chip)

**Created:** 2026-02-05

| Field | Value |
|-------|-------|
| Application ID | 1469048663711158567 |
| Bot Username | [Dev] - Chip#8440 |
| Token Storage | Credential Manager: `Dev_Agent_Discord` |
| Invite URL | https://discord.com/oauth2/authorize?client_id=1469048663711158567&permissions=8&scope=bot |
| Server | Keek Dev Studio |
| Intents | Message Content ✅ |

---

## Credential Storage

### Windows Credential Manager
```powershell
# Store token
cmdkey /generic:PM_Agent_Discord /user:PM_Agent /pass:<TOKEN>

# Retrieve token (PowerShell)
# Requires CredManager helper or P/Invoke
```

### Environment Variable
```bash
export PM_DISCORD_TOKEN="<TOKEN>"
```

### .env File (for Docker)
```
PM_DISCORD_TOKEN=<TOKEN>
```

---

## Troubleshooting

### Bot not responding
1. Check Message Content Intent is enabled
2. Verify bot is online in server member list
3. Check bot has permission to read/send in channel
4. Verify token is correct and not expired

### MFA issues
- Discord requires password verification for sensitive actions
- Use the account password (not 2FA code)
- Password stored in Credential Manager: `Keek_Discord`

### Captcha blocking automation
- hCaptcha may appear during bot creation/authorization
- Click "I am human" checkbox
- May require manual intervention for image challenges
