# Task: Define Persona Schema

**Story**: 02 - Agent Persona Framework  
**Task ID**: STORY_02_TASK_01  
**Status**: Done

## Objective
Document the official persona schema and create the folder structure for persona files.

## Steps

### 1. Create Folder Structure
```
orchestration/
└── config/
    └── agents/
        └── (persona files go here)
```

### 2. Document Schema
Create PERSONA_SCHEMA.md with:
- Required fields
- Optional fields
- Validation rules
- Examples

### 3. Reference DATA_STRUCTURES.md
Schema should align with AgentConfig from architecture docs.

## Expected Outcome
- Folder structure created
- PERSONA_SCHEMA.md in story folder
- Clear documentation for creating new personas

## Acceptance Check
- [x] Folder structure exists
- [x] Schema documented with all fields
- [x] Validation rules defined
- [x] At least one example included

## Results (2026-02-04)
- Created `~/.openclaw/workspace/orchestration/config/agents/`
- Created `~/.openclaw/workspace/orchestration/state/`
- Created `~/.openclaw/workspace/orchestration/logs/`
- Created PERSONA_SCHEMA.md with full field documentation
- Included complete PM example and role templates
