# Task: Configure Email Sending

**Story**: 04 - Configure Email Channel  
**Task ID**: STORY_04_TASK_02  
**Status**: ✅ Complete
**Completed**: 2026-02-04

## Objective
Enable Keek to send emails via SMTP.

## Prerequisites
- ✅ Task 01 complete (App Password generated)

## Implementation

### Discovery
OpenClaw does not have a native email *sending* channel. The `gmail-pubsub` feature is for **receiving** emails via webhooks, not sending.

### Solution
Created a standalone Node.js script using `nodemailer`:

**Script Location**: `C:\Users\Keek_AI\send-email.js`

**Dependencies**:
- `nodemailer` installed locally in `C:\Users\Keek_AI`

**Usage**:
```bash
node C:/Users/Keek_AI/send-email.js --to <email> --subject "Subject" --body "Message body"
```

**Example**:
```bash
node C:/Users/Keek_AI/send-email.js --to esiege@gmail.com --subject "Hello from Keek" --body "This is a test email."
```

### SMTP Configuration (in script)
- **Host**: smtp.gmail.com
- **Port**: 587 (TLS)
- **User**: keek.ai.main@gmail.com
- **Auth**: Google App Password

## Expected Outcome
- ✅ Script can send emails via Gmail SMTP
- ✅ Keek can invoke via `exec` tool

## Future Enhancements
- [ ] Read app password from Credential Manager instead of hardcoding
- [ ] Support HTML email body
- [ ] Support attachments
- [ ] Create skill wrapper for cleaner invocation
- [ ] Consider Gmail API instead of SMTP for better reliability
