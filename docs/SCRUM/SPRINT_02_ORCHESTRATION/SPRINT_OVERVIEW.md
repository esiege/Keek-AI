# Sprint 2: Turn-Based Orchestration Foundation

## Sprint Goal
Build the core infrastructure for multi-agent turn-based coordination in Discord, inspired by tactical RPG mechanics (FFT/Tactics Ogre).

## Acceptance Criteria
**Definition of Done**: Two AI agent personas (PM + Dev) successfully take turns responding to a task in a Discord channel, with clear turn handoffs and state tracking.

**STATUS**: 🔄 IN PROGRESS

## Sprint Duration
Target: 1-2 weeks
Started: 2026-02-04

## Stories

| ID | Story | Points | Status |
|----|-------|--------|--------|
| S01 | Define Orchestration Architecture | 3 | In Progress |
| S02 | Create Agent Persona Framework | 5 | TODO |
| S03 | Build Turn Manager | 8 | TODO |
| S04 | Discord Channel Routing | 5 | TODO |
| S05 | Validate with 2-Agent Demo | 3 | TODO |

**Total Points**: 24

---

## Core Concepts

### Tactical RPG Inspiration
Like Final Fantasy Tactics or Tactics Ogre:
- **Turn Order**: Agents take turns in a defined sequence
- **Initiative/Speed**: Some agents can act more frequently
- **Reactions**: Agents can interrupt on specific triggers
- **Wait**: Agents can pass and defer their turn
- **Charge Time**: Agents can announce intent, execute later

### Key Design Decisions

**Channel = Context**
- Moving an agent into a channel assigns them to that work
- Channel history provides shared context
- Multiple agents in one channel = collaboration

**Turn = Complete Thought**
- One agent acts at a time (no collision)
- Agent signals "turn complete" explicitly
- State tracked in files, not just chat

**Extensible Rules**
- Start simple (round-robin)
- Add complexity later (priority, triggers, interrupts)

---

## Architecture Preview

```
┌─────────────────────────────────────────────────┐
│              Turn Manager (Orchestrator)         │
├─────────────────────────────────────────────────┤
│  - Maintains turn queue                          │
│  - Tracks agent states (idle/active/waiting)     │
│  - Handles turn signals                          │
│  - Routes messages to active agent               │
└─────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────┐
│              Agent Registry                      │
├─────────────────────────────────────────────────┤
│  Agent: PM                                       │
│  - Role: Product Manager                         │
│  - Priority: 2                                   │
│  - Channels: [#planning, #reviews]               │
│                                                  │
│  Agent: Dev                                      │
│  - Role: Developer                               │
│  - Priority: 1                                   │
│  - Channels: [#dev-work, #reviews]               │
└─────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────┐
│              Discord Channels                    │
├─────────────────────────────────────────────────┤
│  #planning     → PM only                         │
│  #dev-work     → Dev only                        │
│  #reviews      → PM + Dev (turn-based)           │
│  #admin-chat   → Human + Orchestrator            │
└─────────────────────────────────────────────────┘
```

---

## Open Questions

1. **Turn Signal**: How does an agent signal "my turn is done"?
   - Special keyword? (`TURN_COMPLETE`)
   - Timeout after X seconds of silence?
   - Explicit handoff to next agent?

2. **Human Interrupts**: Can humans inject messages mid-turn?
   - Always allowed (pause agent turn)?
   - Queue until turn complete?
   - Immediate interrupt (agent yields)?

3. **State Persistence**: Where does turn state live?
   - Memory file? (`orchestration-state.json`)
   - Discord channel topic?
   - Both?

4. **Multi-Channel**: Can one agent be in multiple channels?
   - Yes, but only active in one at a time?
   - Parallel processing later?

---

## Dependencies

- ✅ OpenClaw installed and running
- ✅ Discord bot connected
- ✅ Basic messaging working
- ⏳ Multiple Discord bot accounts (or webhook-based personas?)

---

## Notes

- This sprint focuses on **foundation** — fancy turn rules come later
- Start with 2 agents to prove the concept
- File-based state keeps things debuggable
- Discord is the "game board" — visual and interactive
