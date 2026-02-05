# Acceptance Criteria: Story 05 - Two-Agent Demo

## Functional Requirements

### AC-1: Demo Environment Ready
**GIVEN** the orchestration system is built
**WHEN** demo preparation is complete
**THEN** the following must be true:
- [ ] PM persona loaded and functional
- [ ] Dev persona loaded and functional
- [ ] Turn manager running
- [ ] #reviews channel configured for turn-based mode
- [ ] State file initialized

### AC-2: Turn Order Respected
**GIVEN** PM and Dev are in a turn-based channel
**WHEN** a task is posted
**THEN** the following must occur:
- [ ] PM responds first (as first in queue)
- [ ] Dev does NOT respond during PM's turn
- [ ] After PM signals complete, Dev becomes active
- [ ] PM does NOT respond during Dev's turn

### AC-3: Turn Completion Works
**GIVEN** an agent is active
**WHEN** they include TURN_COMPLETE in their message
**THEN** the system must:
- [ ] Detect the signal
- [ ] End the agent's turn
- [ ] Advance to next agent
- [ ] Update state file

### AC-4: State Persistence Verified
**GIVEN** turns are being taken
**WHEN** the state file is inspected
**THEN** it must:
- [ ] Show correct current agent
- [ ] Show accurate queue position
- [ ] Show accurate timestamps
- [ ] Match what's happening in Discord

### AC-5: Product Owner Sign-Off
**GIVEN** the demo is complete
**WHEN** Jesse reviews the results
**THEN**:
- [ ] Demo meets expectations
- [ ] No critical issues identified
- [ ] Sign-off recorded in this file

---

## Demo Checklist

### Pre-Demo
- [ ] All systems running
- [ ] State cleared/reset
- [ ] Screen recording ready (optional)
- [ ] Demo script reviewed

### During Demo
- [ ] Follow demo script
- [ ] Note any deviations
- [ ] Capture any errors
- [ ] Time the turn transitions

### Post-Demo
- [ ] Review state file
- [ ] Review logs
- [ ] Document issues found
- [ ] Get Jesse's feedback

---

## Sign-Off Section

**Demo Date**: _______________

**Demo Participants**: 
- Jesse (Product Owner)
- Keek (Orchestrator)

**Result**: ⬜ PASS / ⬜ FAIL

**Issues Found**:
1. 
2. 
3. 

**Jesse's Notes**:
> (feedback goes here)

**Sign-Off**: ⬜ Approved / ⬜ Needs Work

---

## Definition of Done

- [ ] Demo executed successfully
- [ ] All acceptance criteria met
- [ ] Issues documented (if any)
- [ ] Jesse has signed off
- [ ] Sprint 2 can be marked complete
