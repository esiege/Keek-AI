# Acceptance Criteria: Story 03 - Turn Manager

## Functional Requirements

### AC-1: Turn Queue Management
**GIVEN** multiple agents are registered
**WHEN** the turn manager initializes
**THEN** it must:
- [ ] Load agent list from configuration
- [ ] Establish initial turn order
- [ ] Set first agent to ACTIVE state
- [ ] Set remaining agents to QUEUED state

### AC-2: Turn Advancement
**GIVEN** an agent completes their turn
**WHEN** turn completion is signaled (keyword or timeout)
**THEN** the turn manager must:
- [ ] Set current agent to IDLE or QUEUED
- [ ] Advance queue index (wrap at end)
- [ ] Set next agent to ACTIVE
- [ ] Log the turn transition
- [ ] Update state file

### AC-3: Turn Completion Detection
**GIVEN** an agent is in ACTIVE state
**WHEN** the agent signals completion
**THEN** the system must:
- [ ] Recognize `TURN_COMPLETE` keyword in message
- [ ] OR trigger timeout after configured duration
- [ ] Strip the keyword from displayed message (if present)
- [ ] Initiate turn advancement

### AC-4: State Persistence
**GIVEN** turn state needs to survive restarts
**WHEN** any state change occurs
**THEN** the system must:
- [ ] Write current state to `orchestration-state.json`
- [ ] Include current turn info (agent, start time, timeout)
- [ ] Include full queue and index
- [ ] Include agent states
- [ ] Load state on startup (resume where left off)

### AC-5: Timeout Handling
**GIVEN** an agent may not respond
**WHEN** timeout duration is exceeded
**THEN** the turn manager must:
- [ ] Automatically end the agent's turn
- [ ] Log timeout event
- [ ] Optionally post timeout notice to channel
- [ ] Advance to next agent

---

## Non-Functional Requirements

- Turn transitions should complete in < 1 second
- State file writes should be atomic (no corruption)
- Logs should be sufficient to debug turn issues

---

## Definition of Done

- [ ] Turn manager logic implemented
- [ ] Round-robin turn order working
- [ ] Keyword detection working
- [ ] Timeout fallback working
- [ ] State file persists across restarts
- [ ] Logging captures all turn events
- [ ] Manual testing with 2 agents successful
