# Acceptance Criteria: Story 02 - Agent Persona Framework

## Acceptance Criteria

**AC-1: Distinct Agent Appearance**
- GIVEN PM and Dev are configured as personas
- WHEN they post messages in Discord
- THEN they appear as separate users with distinct names and avatars

**AC-2: PM Identity**
- GIVEN PM posts a message
- THEN the message shows:
  - Username: "PM"
  - Unique avatar (not Keek AI's avatar)

**AC-3: Dev Identity**  
- GIVEN Dev posts a message
- THEN the message shows:
  - Username: "Dev"
  - Unique avatar (different from PM and Keek AI)

---

## How to Test

1. Go to #admin-chat (or any channel with webhook)
2. Trigger PM to post a message
3. Verify: Name shows "PM", avatar is PM's icon
4. Trigger Dev to post a message
5. Verify: Name shows "Dev", avatar is Dev's icon

---

## Definition of Done

- [x] Webhook created for agent personas
- [x] PM can post with distinct identity
- [x] Dev can post with distinct identity
- [x] Webhook URL saved in TOOLS.md
- [ ] Persona configs include avatar URLs
- [ ] Jesse reviewed and approved

---

## Implementation Notes

**Approach**: Discord webhooks (not multiple bots)

**Why webhooks**:
- Single bot, unlimited personas
- Each message can have custom username + avatar
- Scales to 100+ agents without hitting Discord limits
- Simpler infrastructure

**Webhook URL**: See TOOLS.md
