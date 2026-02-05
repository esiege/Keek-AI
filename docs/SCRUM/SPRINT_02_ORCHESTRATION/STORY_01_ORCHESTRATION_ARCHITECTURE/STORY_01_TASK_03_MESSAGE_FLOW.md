# Task: Document Message Flow

**Story**: 01 - Orchestration Architecture  
**Task ID**: STORY_01_TASK_03  
**Status**: Done

## Objective
Create sequence diagrams showing how messages flow through the orchestration system.

## Steps

### 1. Basic Message Flow
Document: User posts message → Agent responds
- Discord receives message
- OpenClaw processes
- Routing to correct agent
- Agent generates response
- Response posted to Discord

### 2. Turn Handoff Flow
Document: Agent A completes → Agent B becomes active
- Agent A signals TURN_COMPLETE
- Turn manager detects signal
- State updated
- Agent B notified/activated
- Next message routes to Agent B

### 3. Human Interrupt Flow
Document: Human posts while agent is mid-turn
- Options: queue, interrupt, ignore
- How does system handle?
- What happens to agent's turn?

### 4. Error/Timeout Flow
Document: Agent fails or times out
- Timeout detection
- Error recovery
- Turn advancement despite failure
- Logging and alerting

## Expected Outcome
- `MESSAGE_FLOW.md` document in story folder
- Sequence diagrams (Mermaid or ASCII)
- Clear flow for each scenario

## Acceptance Check
- [x] Basic message flow documented
- [x] Turn handoff flow documented
- [x] Human interrupt flow documented
- [x] Error/timeout flow documented
- [x] Diagrams are clear and readable

## Results (2026-02-04)
Created `MESSAGE_FLOW.md` with:
- 6 sequence diagrams (ASCII art)
- Basic message flow: user → agent → response
- Turn handoff: TURN_COMPLETE detection and queue advancement
- Timeout flow: forced turn end with notice
- Human interrupt: 3 modes (inject, queue, interrupt)
- Error recovery: crash handling and queue continuity
- Multi-agent collaboration example (PM + Dev)
