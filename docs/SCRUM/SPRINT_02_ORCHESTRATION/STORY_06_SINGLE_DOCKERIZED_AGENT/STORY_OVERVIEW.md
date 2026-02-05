# Story 06: Single Dockerized Agent (Proof of Concept)

## User Story
*As an orchestrator, I need one agent running in Docker with its own Discord bot, so I can validate the containerized agent architecture.*

## Story Points: 5

## Status: TODO

## Description
This story proves out the Docker-based agent architecture. We create a single agent (PM) running in its own container with its own OpenClaw instance and Discord bot identity. This validates:

- OpenClaw can run in Docker
- Container can connect to Discord as a unique bot
- Agent has isolated context (own SOUL.md, workspace)
- Agent persona is distinct from Keek

## Why Docker?
- **True isolation**: Separate filesystem, process space, network namespace
- **Reproducible**: Dockerfile defines exact environment
- **Scalable**: Same pattern for 2 agents or 20
- **Industry standard**: Proven architecture for multi-service systems

## Dependencies
- Docker Desktop installed on NucBox
- Discord Developer Portal access (to create new bot application)
- OpenClaw base image (or Dockerfile to create one)

## Deliverables
1. Dockerfile for agent container
2. docker-compose.yml for single agent
3. PM Discord bot application (in Discord Developer Portal)
4. PM workspace with SOUL.md
5. Working demo: PM responds in Discord as distinct identity

## Tasks
- [ ] Task 01: Install Docker Desktop on NucBox
- [ ] Task 02: Create Discord bot application for PM
- [ ] Task 03: Create OpenClaw Dockerfile
- [ ] Task 04: Create PM agent workspace and SOUL.md
- [ ] Task 05: Configure and test PM container

## Notes
- Focus on proving the pattern works, not perfection
- PM personality should be visibly different from Keek
- Document all manual steps for Story 07 automation
