# Task: Define State Machine

**Story**: 01 - Orchestration Architecture  
**Task ID**: STORY_01_TASK_01  
**Status**: TODO

## Objective
Design and document the agent state machine — all possible states and transitions.

## Steps

### 1. Define Agent States
Document each state an agent can be in:
- IDLE — not assigned to any active work
- QUEUED — assigned to channel, waiting for turn
- ACTIVE — currently has the turn, can respond
- WAITING — paused mid-turn (blocked, needs input)

### 2. Define State Transitions
For each state, document:
- What triggers entry into this state
- What triggers exit from this state
- Valid next states

### 3. Create State Diagram
Visual diagram showing:
- All states as nodes
- Transitions as arrows with labels
- Entry/exit conditions

### 4. Document Edge Cases
- What if agent crashes mid-turn?
- What if timeout during ACTIVE?
- What if human interrupts?
- What if agent never signals done?

## Expected Outcome
- `STATE_MACHINE.md` document in story folder
- Clear, unambiguous state definitions
- Visual diagram included

## Acceptance Check
- [ ] All states defined with descriptions
- [ ] All transitions documented
- [ ] Diagram created (ASCII or Mermaid)
- [ ] Edge cases addressed
