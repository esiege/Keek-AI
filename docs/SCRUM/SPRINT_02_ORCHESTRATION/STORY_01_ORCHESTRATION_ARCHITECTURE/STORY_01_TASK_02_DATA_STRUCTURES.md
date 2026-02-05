# Task: Define Data Structures

**Story**: 01 - Orchestration Architecture  
**Task ID**: STORY_01_TASK_02  
**Status**: Done

## Objective
Design JSON schemas for all orchestration data: agent config, turn queue, state persistence.

## Steps

### 1. Agent Configuration Schema
Define the structure for persona/agent config:
```json
{
  "id": "string",
  "name": "string",
  "role": "string",
  "priority": "number",
  "systemPrompt": "string",
  "capabilities": {},
  "channels": [],
  "turnBehavior": {}
}
```

### 2. Turn Queue Schema
Define the structure for turn management:
```json
{
  "queue": ["agent_id", ...],
  "currentIndex": "number",
  "mode": "round-robin | priority"
}
```

### 3. State File Schema
Define the full orchestration state:
```json
{
  "currentTurn": {},
  "queue": [],
  "agents": {},
  "history": []
}
```

### 4. Provide Examples
Create example instances for each schema showing real data.

## Expected Outcome
- `DATA_STRUCTURES.md` document in story folder
- Valid JSON schemas with comments
- Example instances for each

## Acceptance Check
- [x] Agent config schema defined
- [x] Turn queue schema defined (part of channel config)
- [x] State file schema defined
- [x] Examples provided for each
- [x] Schemas are valid JSON

## Results (2026-02-04)
Created `DATA_STRUCTURES.md` with:
- AgentConfig schema with full persona definition
- ChannelConfig schema with routing modes
- OrchestrationState schema for runtime persistence
- Example PM and Dev agent configs
- File location conventions
- Validation rules
