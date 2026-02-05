# Story 02: Create Agent Persona Framework

**Story ID**: S02
**Story Points**: 5
**Status**: TODO
**Sprint**: Sprint 2 - Orchestration

---

## User Story

As a **studio operator**, I want to **define agent personas with distinct roles, personalities, and capabilities** so that **each agent behaves appropriately for their function**.

---

## Description

Each agent in our studio has a role (PM, Dev, QA, Ops, etc.). This story creates the framework for defining these personas:

1. **Persona Definition**: What makes an agent unique (role, personality, expertise)
2. **Configuration Format**: How personas are stored and loaded
3. **Prompt Templates**: Base prompts that define agent behavior
4. **Capability Flags**: What each agent can/cannot do

---

## Deliverables

1. `personas/` folder structure in workspace
2. `PERSONA_SCHEMA.md` — Documentation of persona format
3. `pm.json` — Product Manager persona definition
4. `dev.json` — Developer persona definition
5. Persona loading utility (or documentation of how OpenClaw handles this)

---

## Technical Approach

### Persona Structure

```json
{
  "id": "pm",
  "name": "Product Manager",
  "role": "PM",
  "priority": 2,
  "personality": {
    "tone": "organized, strategic, user-focused",
    "style": "asks clarifying questions, thinks in requirements"
  },
  "systemPrompt": "You are a Product Manager...",
  "capabilities": {
    "canCreateStories": true,
    "canWriteCode": false,
    "canApproveWork": true,
    "canDeploy": false
  },
  "channels": ["planning", "reviews"],
  "turnBehavior": {
    "defaultTimeout": 60,
    "canInterrupt": false,
    "interruptTriggers": ["blocker", "urgent"]
  }
}
```

### Key Personas for MVP

| Persona | Role | Key Behaviors |
|---------|------|---------------|
| PM | Product Manager | Writes specs, clarifies requirements, prioritizes |
| Dev | Developer | Writes code, asks technical questions, implements |

### Future Personas (Out of Scope for This Sprint)

- QA Engineer
- Ops/DevOps
- Tech Lead
- Designer

---

## Integration Points

- **OpenClaw Sessions**: Each persona might be a separate session or sub-agent
- **Discord Identity**: How does each persona appear in Discord? Same bot? Webhooks?
- **Turn Manager**: Personas feed into turn priority and routing

---

## Dependencies

- Story 01 (Architecture) should inform persona capabilities

---

## Open Questions

1. Do personas run as separate OpenClaw sessions or one session with role-switching?
2. Can we use Discord webhooks to give each persona a unique name/avatar?
3. How much personality is too much? Should they be functional-only?
