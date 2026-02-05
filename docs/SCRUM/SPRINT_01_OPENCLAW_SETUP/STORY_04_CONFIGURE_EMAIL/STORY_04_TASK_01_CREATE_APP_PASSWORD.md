# Task: Create Gmail App Password

**Story**: 04 - Configure Email Channel  
**Task ID**: STORY_04_TASK_01  
**Status**: ✅ Complete
**Completed**: 2026-02-04

## Objective
Generate a Gmail App Password that allows OpenClaw to send emails via SMTP without requiring browser-based OAuth.

## Prerequisites
- Gmail account with 2-Factor Authentication enabled
- Access to Google Account security settings

## Steps

### 1. Enable 2-Factor Authentication (if not already)
- Go to https://myaccount.google.com/security
- Under "Signing in to Google", ensure 2-Step Verification is ON
- If not, set it up first (required for App Passwords)

### 2. Generate App Password
- Go to https://myaccount.google.com/apppasswords
- Select app: "Mail"
- Select device: "Other (Custom name)"
- Name it: "OpenClaw Keek" (or similar)
- Click "Generate"

### 3. Save the Password
- Copy the 16-character password shown
- **Store it securely** — you won't see it again
- Recommended: Save to `~/.openclaw/secrets/` or environment variable

### 4. Test Credentials (Optional)
Verify credentials work with a simple SMTP test before configuring OpenClaw.

## Expected Outcome
- 16-character App Password generated
- Password stored securely for use in Task 02

## Security Notes
- App Passwords bypass 2FA for specific apps
- Can be revoked anytime from Google Account settings
- Never commit to git or share in plain text
- If compromised, revoke immediately and generate new one

## Decision Point
**Which Gmail account to use?**
- ✅ `keek.ai.main@gmail.com` — Keek's dedicated Gmail (CHOSEN)

## Completion Notes
- **2FA enabled**: Authenticator + Phone (971-517-7438)
- **App Password created**: Named "OpenClaw SMTP"
- **Stored in**: Windows Credential Manager (`Keek_Google_AppPassword`)
- **App Password**: `uqit fmcu hcnm gofo` (16 chars, no spaces when used)
