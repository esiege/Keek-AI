# Story 07: Reusable Agent Container Template

## User Story
*As an orchestrator, I need a repeatable way to create new agent bots, so I can scale the team without manual setup each time.*

## Story Points: 8

## Status: TODO

## Description
Building on Story 06, this story creates a reusable template for agent containers. The goal is to make adding a new agent as simple as:
1. Create Discord bot (documented or scripted)
2. Define agent persona (SOUL.md + config)
3. Add entry to docker-compose
4. `docker-compose up <agent-name>`

## Scope
- Base Docker image with OpenClaw pre-configured
- Template workspace structure for new agents
- Documentation (or scripts) for Discord bot creation
- docker-compose pattern for multi-agent deployment
- At least 2 working agents (PM + Dev) as proof

## Dependencies
- Story 06 complete (single agent working)
- Discord Developer Portal access

## Deliverables
1. Base agent Docker image (pushed to local or registry)
2. Agent workspace template (`/templates/agent-workspace/`)
3. Discord bot setup guide (or automation script)
4. Multi-agent docker-compose.yml
5. Working PM + Dev agents simultaneously

## Tasks
- [ ] Task 01: Refactor S06 Dockerfile into reusable base image
- [ ] Task 02: Create agent workspace template
- [ ] Task 03: Document/script Discord bot creation
- [ ] Task 04: Create Dev agent (second bot)
- [ ] Task 05: Multi-agent docker-compose configuration
- [ ] Task 06: Test both agents running simultaneously

## Agent Template Structure
```
/agents/<agent-name>/
├── workspace/
│   ├── SOUL.md           # Agent personality
│   ├── MEMORY.md         # Agent's memories (starts empty)
│   └── TOOLS.md          # Agent-specific tool notes
├── config/
│   └── openclaw.json     # Agent's OpenClaw config (Discord token, etc.)
└── .env                  # Environment variables (tokens, etc.)
```

## Future Considerations
- Private Docker registry for agent images
- Automated Discord bot creation via API (if possible)
- Agent versioning (tag images with version)
- Kubernetes deployment for true scalability
