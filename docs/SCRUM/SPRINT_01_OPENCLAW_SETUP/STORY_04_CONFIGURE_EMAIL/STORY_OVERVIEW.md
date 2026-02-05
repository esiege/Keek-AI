# Story 04: Configure Email Channel

**Status**: ✅ Complete
**Completed**: 2026-02-04

## Story
As Keek, I want to send emails to Jesse so that I can deliver information to devices/locations outside my immediate environment.

## Context
Jesse has their own PC; I run on NucBox_M7Pro. When Jesse is at my machine helping with setup, they sometimes need information sent to their own workstation. Email bridges this physical gap and provides a persistent, searchable communication channel.

## Acceptance Criteria
- [x] ~~Gmail SMTP configured in OpenClaw~~ (OpenClaw has no native email channel)
- [x] Keek can send email via script (invokable via `exec` tool)
- [x] Test email received by Jesse at esiege@gmail.com
- [x] Email sending works reliably

## Story Points: 3

## Tasks

| ID | Task | Status |
|----|------|--------|
| T01 | Create Gmail App Password | ✅ Complete |
| T02 | Configure Email Sending | ✅ Complete |
| T03 | Test Email Delivery | ✅ Complete |

## Implementation Summary

**Discovery**: OpenClaw does not have a native email *sending* channel. The `gmail-pubsub` docs are for *receiving* only.

**Solution**: Created `C:\Users\Keek_AI\send-email.js` using `nodemailer`:
```bash
node C:/Users/Keek_AI/send-email.js --to <email> --subject "Subject" --body "Body"
```

**Gmail Setup**:
- Account: keek.ai.main@gmail.com
- 2FA: Enabled (Authenticator + Phone)
- App Password: Created for "OpenClaw SMTP"

## Technical Notes
- Uses Gmail SMTP (`smtp.gmail.com:587`)
- App Password stored in script (future: use Credential Manager)
- Script in `C:\Users\Keek_AI\` with local `nodemailer` dependency

## Design Decisions Made
1. **Sending address**: keek.ai.main@gmail.com ✅
2. **Scope**: Send-only for now ✅
3. **Security**: App Password in script (acceptable for now, future enhancement to use Credential Manager)

## Why This Matters
Email is infrastructure. Setting it up once means:
- Keek can notify Jesse across device boundaries
- Links, reports, summaries can be delivered without UI automation
- Async communication when Jesse isn't watching the chat

✅ 30 minutes invested, email capability unlocked forever.
