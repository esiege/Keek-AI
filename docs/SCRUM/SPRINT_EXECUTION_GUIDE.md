# Sprint Execution Guide

**For**: Keek AI Multi-Role Development Studio  
**Purpose**: Quick reference for executing sprints

---

## Sprint Structure

```
SPRINT_XX_NAME/
├── SPRINT_OVERVIEW.md          # Sprint goal, acceptance criteria, story list
├── STORY_01_NAME/
│   ├── STORY_OVERVIEW.md       # Story description, acceptance criteria
│   ├── STORY_01_TASK_01_*.md   # Individual task files
│   ├── STORY_01_TASK_02_*.md
│   └── ...
├── STORY_02_NAME/
│   └── ...
└── STORY_03_NAME/
    └── ...
```

---

## Workflow Per Task

1. **Read task file** - understand what needs doing
2. **Execute** - run commands, configure, implement
3. **Verify** - confirm task output matches expected outcome
4. **Update status** - mark task done in the file
5. **Commit** - save progress with task reference
6. **Next task** - repeat

---

## Story Flow

- Complete all tasks in a story before moving to the next
- Stories may have dependencies (check STORY_OVERVIEW.md)
- Story is DONE when all its tasks pass acceptance criteria

---

## Sprint Complete When

The sprint's acceptance criteria (defined in SPRINT_OVERVIEW.md) is met.

---

## Practical Execution

### For Interactive Setup Tasks
- Execute each task step-by-step
- Update task files as we complete them
- Commit after each story completion
- User handles manual steps (portals, API keys, etc.)

### For Coding Tasks
- Read task specification
- Implement per spec
- Run tests
- Update task status
- Commit with story/task reference

---

## Task Status Updates

Update the task file's status field when:
- Starting: `Not Started` → `In Progress`
- Completing: `In Progress` → `Done`
- Blocked: Add `Blocked: [reason]` note

---

## Commit Convention

```
feat(STORY_XX): brief description

- Task MM completed
- Details of what was done
```

Example:
```
feat(STORY_01): install OpenClaw successfully

- Task 01: Prerequisites verified
- Task 02: Installer completed
- Task 03: Persona configured
```

---

## Interruptions & Bugs

See [BUG_TRACKING.md](BUG_TRACKING.md) for handling bugs discovered during sprint execution.
