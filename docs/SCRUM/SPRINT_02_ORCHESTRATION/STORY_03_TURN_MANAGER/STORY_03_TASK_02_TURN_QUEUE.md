# Task: Implement Turn Queue Logic

**Story**: 03 - Build Turn Manager  
**Task ID**: STORY_03_TASK_02  
**Status**: Done

## Objective
Implement the core round-robin turn queue logic.

## Steps

### 1. Queue Initialization
- Load agents assigned to channel
- Establish initial turn order
- Set first agent to ACTIVE

### 2. Turn Advancement
- Increment queue index
- Wrap at end of queue
- Update agent states (previous→QUEUED, next→ACTIVE)

### 3. Queue Manipulation
- Add agent to queue
- Remove agent from queue
- Reorder queue (future)

### 4. Document Algorithm
- Pseudocode for each operation
- Edge cases handled

## Expected Outcome
- Turn queue logic fully specified
- Round-robin advancement works correctly
- Agent add/remove handled gracefully

## Acceptance Check
- [x] Queue initialization documented
- [x] Turn advancement logic clear
- [x] Add/remove operations defined
- [x] Edge cases addressed

## Results (2026-02-04)
Created TURN_QUEUE.md with:
- initializeQueue, advanceTurn functions
- addAgentToQueue, removeAgentFromQueue
- Helper functions (getQueuePosition, getTurnsUntil)
- Edge cases: empty queue, single agent, active agent removal
