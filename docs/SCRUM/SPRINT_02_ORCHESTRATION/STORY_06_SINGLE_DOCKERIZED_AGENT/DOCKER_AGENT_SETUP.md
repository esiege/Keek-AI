# Docker Agent Setup Guide

Complete instructions for running an OpenClaw agent in Docker with its own Discord bot identity.

## Prerequisites

- Docker Desktop installed (with WSL2 backend on Windows)
- Discord bot application created (see DISCORD_BOT_SETUP.md)
- Anthropic API key

## Directory Structure

```
orchestration/
├── docker/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── entrypoint.sh
└── agents/
    └── pm/                          # One folder per agent
        ├── openclaw.json            # Agent config
        ├── workspace/
        │   └── SOUL.md              # Agent personality
        └── agents/
            └── main/
                ├── agent/
                │   └── auth-profiles.json   # API keys
                └── sessions/                # Session storage
```

## Step 1: Create Dockerfile

**IMPORTANT**: OpenClaw requires **Node 22+**

```dockerfile
# OpenClaw Agent Base Image
FROM node:22

# Install OpenClaw
RUN npm install -g openclaw@2026.2.3-1

# Verify
RUN openclaw --version

# Create agent user
RUN useradd -m -s /bin/bash agent && \
    mkdir -p /home/agent/.openclaw/workspace && \
    chown -R agent:agent /home/agent/.openclaw

WORKDIR /home/agent/.openclaw

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

USER agent

ENTRYPOINT ["/entrypoint.sh"]
```

## Step 2: Create entrypoint.sh

**IMPORTANT**: Must have Unix line endings (LF, not CRLF)

```bash
#!/bin/bash
set -e

# Ensure config exists
if [ ! -f "/home/agent/.openclaw/openclaw.json" ]; then
    echo "Error: openclaw.json not found. Mount the config file."
    exit 1
fi

# Start OpenClaw gateway
exec /usr/local/bin/openclaw gateway
```

## Step 3: Create docker-compose.yml

```yaml
services:
  pm:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: pm-agent
    volumes:
      # Mount agent-specific config
      - ../agents/pm/openclaw.json:/home/agent/.openclaw/openclaw.json
      # Mount full agents directory (includes auth + sessions)
      - ../agents/pm/agents:/home/agent/.openclaw/agents
      # Mount agent workspace
      - ../agents/pm/workspace:/home/agent/.openclaw/workspace
    restart: unless-stopped
    networks:
      - agent-network

networks:
  agent-network:
    driver: bridge
```

## Step 4: Create Agent Config (openclaw.json)

```json
{
  "auth": {
    "profiles": {
      "anthropic:default": {
        "provider": "anthropic",
        "mode": "api_key"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-20250514"
      },
      "workspace": "/home/agent/.openclaw/workspace"
    }
  },
  "channels": {
    "discord": {
      "enabled": true,
      "allowBots": true,
      "token": "<DISCORD_BOT_TOKEN>",
      "groupPolicy": "open",
      "guilds": {
        "<GUILD_ID>": {
          "slug": "server-name",
          "channels": {
            "<CHANNEL_ID>": {
              "allow": true,
              "requireMention": false
            }
          }
        }
      }
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "random-token-for-this-agent"
    }
  },
  "plugins": {
    "entries": {
      "discord": {
        "enabled": true
      }
    }
  }
}
```

## Step 5: Create auth-profiles.json

Location: `agents/pm/agents/main/agent/auth-profiles.json`

```json
{
  "version": 1,
  "profiles": {
    "anthropic:default": {
      "type": "api_key",
      "provider": "anthropic",
      "key": "<ANTHROPIC_API_KEY>"
    }
  },
  "lastGood": {
    "anthropic": "anthropic:default"
  }
}
```

## Step 6: Create SOUL.md

Location: `agents/<agent>/workspace/SOUL.md`

This defines the agent's personality. Use the template at:
`STORY_07_REUSABLE_AGENT_TEMPLATE/AGENT_SOUL_TEMPLATE.md`

Example for PM Agent:

```markdown
# SOUL.md - PM Agent

You are **PM Agent**, the Product Manager for the team.

## Your Role
- Define requirements and acceptance criteria
- Prioritize work and manage the backlog
- Coordinate between stakeholders and development

## Your Personality
- Professional and organized
- Detail-oriented
- Decisive

## Turn Protocol
When your turn is complete, end your message with:
TURN_COMPLETE
```

## Step 7: Build and Run

```bash
cd orchestration/docker

# Build the image
docker build -t openclaw-agent:base .

# Start the agent
docker-compose up pm -d

# Check logs
docker logs pm-agent --tail 50

# Verify Discord connection
# Look for: "[discord] logged in to discord as <BOT_ID>"
```

## Step 8: Fix Permissions (if needed)

If you see permission errors like `EACCES` or `EPERM`:

```bash
docker exec -u root pm-agent chown -R agent:agent /home/agent/.openclaw/agents
docker exec -u root pm-agent chmod -R 755 /home/agent/.openclaw/agents
```

## Step 9: Approve User Pairing

When a user first messages the bot, they'll get a pairing code. Approve it:

```bash
docker exec pm-agent openclaw pairing approve discord <CODE>
```

## Multi-Agent Communication

**CRITICAL**: For agents to see each other's messages, add `allowBots: true` to the Discord config:

```json
"channels": {
  "discord": {
    "enabled": true,
    "allowBots": true,  // Required for multi-agent setups!
    ...
  }
}
```

Without this setting, OpenClaw filters out bot messages by default (to prevent loops). In a multi-agent setup where you WANT agents to communicate, you need this enabled.

**Warning**: With `allowBots: true`, ensure agents don't create reply loops. Use clear turn protocols (like `TURN_COMPLETE`) and/or `requireMention` to control who responds.

## Troubleshooting

### "openclaw: not found"
- Ensure Dockerfile uses `node:22` (not node:20)
- OpenClaw requires Node 22.12.0+

### "channels unresolved"
- Bot may need time to sync with Discord
- Verify bot has access to the channels
- Check bot was invited with correct permissions

### "EACCES: permission denied"
- Run the permission fix commands above
- Ensure volumes are mounted without `:ro` flag

### "Discord configured, not enabled yet"
- Add `plugins.entries.discord.enabled: true` to config

### Agent not responding
- Check logs: `docker logs pm-agent`
- Verify pairing is approved
- Ensure auth-profiles.json has valid API key

## Channel IDs Reference (Keek Dev Studio)

| Channel | ID | PM Access |
|---------|-----|-----------|
| #admin-chat | 1468824206677381210 | ❌ No |
| #general | 1468820135925125317 | ✅ Yes |
| #development | 1468894176132337807 | ✅ Yes |

## PM Agent Details

| Field | Value |
|-------|-------|
| Bot ID | 1469026037110407340 |
| Container | pm-agent |
| Image | docker-pm / openclaw-agent:base |
| Model | claude-sonnet-4 |
