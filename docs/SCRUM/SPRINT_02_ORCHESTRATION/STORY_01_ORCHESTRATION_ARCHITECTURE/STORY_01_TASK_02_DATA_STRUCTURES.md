# Task: Define Data Structures

**Story**: 01 - Orchestration Architecture  
**Task ID**: STORY_01_TASK_02  
**Status**: TODO

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
- [ ] Agent config schema defined
- [ ] Turn queue schema defined
- [ ] State file schema defined
- [ ] Examples provided for each
- [ ] Schemas are valid JSON
