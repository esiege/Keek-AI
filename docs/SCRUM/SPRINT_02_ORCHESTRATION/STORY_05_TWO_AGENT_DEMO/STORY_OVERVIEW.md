# Story 05: Validate with 2-Agent Demo

**Story ID**: S05
**Story Points**: 3
**Status**: Done
**Sprint**: Sprint 2 - Orchestration

---

## Tasks

| Task | Description | Status |
|------|-------------|--------|
| TASK_01 | Create Demo Script | Done |

---

## User Story

As the **product owner (Jesse)**, I want to **see PM and Dev agents take turns on a real task** so that **I can validate the orchestration system works end-to-end**.

---

## Description

This is the validation story — prove the system works with a real scenario. 

**Demo Scenario**: 
> "Create a simple feature spec and implement it"

1. Human posts task in #reviews channel
2. PM takes first turn — writes a brief spec
3. PM signals turn complete
4. Dev takes second turn — implements (or describes implementation)
5. Dev signals turn complete
6. Cycle continues until task is "done"

---

## Deliverables

1. Working demo captured (screenshots or recording)
2. Demo script documenting what was tested
3. List of issues found during demo
4. Sign-off from Jesse

---

## Demo Script

### Setup
1. Ensure both PM and Dev personas are loaded
2. Ensure turn manager is running
3. Ensure #reviews channel is configured for turn-based mode
4. Clear any previous state

### Execution

**Step 1: Human Initiates**
```
[Jesse in #reviews]
"Let's add a /hello command that responds with a greeting. 
PM, please spec this out. Dev, implement after PM is done."
```

**Step 2: PM Turn**
```
[PM responds]
"## /hello Command Spec

**Description**: User types /hello, bot responds with greeting

**Acceptance Criteria**:
- Command is /hello
- Response includes user's name
- Response is friendly

TURN_COMPLETE"
```

**Step 3: Turn Handoff**
- System detects TURN_COMPLETE
- Turn manager advances to Dev
- State file updated

**Step 4: Dev Turn**
```
[Dev responds]
"Got it! Here's the implementation approach:

1. Add slash command handler for /hello
2. Get user's display name from interaction
3. Respond with: "Hey {name}! 👋"

I'll implement this now...
[describes or actually implements]

TURN_COMPLETE"
```

**Step 5: Verify**
- Confirm PM didn't respond during Dev's turn
- Confirm state file shows correct transitions
- Confirm turn history is logged

---

## Success Criteria

| Check | Status |
|-------|--------|
| PM responds first | ⬜ |
| PM's turn ends on TURN_COMPLETE | ⬜ |
| Dev responds second | ⬜ |
| Dev doesn't respond during PM's turn | ⬜ |
| State file accurate | ⬜ |
| Turn transitions logged | ⬜ |
| No crashes or errors | ⬜ |
| Jesse signs off | ⬜ |

---

## Dependencies

- Story 01 (Architecture) — complete
- Story 02 (Personas) — PM and Dev defined
- Story 03 (Turn Manager) — working
- Story 04 (Channel Routing) — #reviews configured

---

## Out of Scope

- Complex turn rules (priority, interrupts)
- More than 2 agents
- Actual code deployment (just demonstrate the flow)

---

## Notes

This is essentially an integration test. If this works, Sprint 2 is a success. If it doesn't, we identify what's broken and fix it before calling the sprint done.
