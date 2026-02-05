# Story 06: Acceptance Criteria

## Criteria

### 1. Docker container runs OpenClaw
- [ ] Container starts without errors
- [ ] OpenClaw gateway is accessible inside container
- [ ] Logs show successful OpenClaw initialization

### 2. Container connects to Discord as distinct bot
- [ ] Bot authenticates with its own token (not Keek AI's token)
- [ ] Bot shows "Online" status in Discord
- [ ] Bot name is NOT "Keek AI" (e.g., "PM Agent" or similar)

### 3. Bot appears in server member sidebar
- [ ] Bot is visible in the member list on the right side of Discord
- [ ] Bot has its own avatar (distinct from Keek)
- [ ] Bot can be @mentioned by name

### 4. Bot responds to messages in #development channel
- [ ] When @mentioned in #development, bot responds
- [ ] Response comes from the PM bot, not Keek
- [ ] Bot can read channel history for context

### 5. Bot has its own SOUL.md personality
- [ ] PM workspace contains SOUL.md with PM-specific personality
- [ ] SOUL.md does NOT include Keek's personal context
- [ ] PM personality is professional/organized (not Keek's vibe)

### 6. Container controllable via docker commands
- [ ] `docker-compose up pm` starts the PM agent
- [ ] `docker-compose down` stops the PM agent
- [ ] `docker-compose logs pm` shows PM container logs

### 7. Responses reflect unique persona
- [ ] PM agent talks like a PM (specs, requirements, priorities)
- [ ] Tone differs from Keek's casual style
- [ ] No references to Keek's memories or personal info

## Test Script

```bash
# 1. Start PM container
docker-compose up -d pm

# 2. Verify bot online
# → Check Discord sidebar for "PM Agent"

# 3. Test response
# → In #development, type: @PM Agent what is your role?
# → Expect: PM-style response about managing product/specs

# 4. Verify isolation
# → Ask: What do you know about Jesse's email?
# → Expect: No knowledge (isolated context)

# 5. Stop container
docker-compose down
# → Bot goes offline in Discord
```

## Definition of Done
All 7 criteria checked off, test script passes, Jesse approves demo.
