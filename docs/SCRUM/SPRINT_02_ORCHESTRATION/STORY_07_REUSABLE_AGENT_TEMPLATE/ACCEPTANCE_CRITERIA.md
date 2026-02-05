# Story 07: Acceptance Criteria

## Criteria

### 1. Base Docker image exists for agent containers
- [ ] Dockerfile builds successfully
- [ ] Image contains OpenClaw and all dependencies
- [ ] Image can be tagged and versioned
- [ ] Image size is reasonable (documented)

### 2. New agent can be created with minimal inputs
Required inputs to create a new agent:
- [ ] Agent name (e.g., "dev", "qa")
- [ ] Discord bot token
- [ ] SOUL.md content (personality definition)

No other configuration should be required for basic operation.

### 3. Discord bot creation is documented or scripted
- [ ] Step-by-step guide exists for creating a Discord bot
- [ ] Guide covers: application creation, bot token generation, required intents
- [ ] Guide covers: inviting bot to server with correct permissions
- [ ] Estimated time to create new bot: < 5 minutes
- [ ] (Stretch) Script automates any steps that can be automated

### 4. docker-compose up starts new agent
- [ ] `docker-compose up <agent-name>` starts the specified agent
- [ ] `docker-compose up` (no args) starts all agents
- [ ] Agents can be started/stopped independently
- [ ] Container names follow consistent pattern

### 5. Agent inherits standard capabilities
- [ ] Discord messaging works
- [ ] Agent has its own isolated workspace (mounted volume)
- [ ] Agent can read/write files in its workspace
- [ ] Agent CANNOT access other agents' workspaces

### 6. Two agents running simultaneously
- [ ] PM agent and Dev agent both online in Discord
- [ ] Both visible in server member sidebar
- [ ] Both respond independently when @mentioned
- [ ] No interference between agents

## Test Script

```bash
# 1. Build base image
docker build -t openclaw-agent:base .

# 2. Start all agents
docker-compose up -d

# 3. Verify both online
# → Discord sidebar shows: "PM Agent", "Dev Agent"

# 4. Test PM
# → @PM Agent describe your role
# → Expect: PM-focused response

# 5. Test Dev
# → @Dev Agent describe your role  
# → Expect: Developer-focused response

# 6. Test isolation
# → @PM Agent what does Dev know?
# → Expect: PM has no access to Dev's context

# 7. Stop one, verify other continues
docker-compose stop pm
# → Dev still responds, PM offline

# 8. Clean up
docker-compose down
```

## Adding a New Agent Checklist
After Story 07, adding a third agent (e.g., QA) should require only:
1. [ ] Create Discord bot in Developer Portal (~5 min)
2. [ ] Create `/agents/qa/` folder from template
3. [ ] Write `SOUL.md` for QA personality
4. [ ] Add QA entry to docker-compose.yml
5. [ ] Add bot token to `.env` or config
6. [ ] `docker-compose up qa`

## Definition of Done
All 6 criteria checked off, both agents demonstrated running, Jesse approves.
