# Rejection Routing Flowchart

## Story Rejection Decision Tree

### When QA Rejects a Story

**Step 1: Classify the Rejection Type**

#### Type A: Implementation Bugs/Defects
- Code doesn't work as specified in acceptance criteria
- Technical implementation errors
- Performance issues not meeting defined criteria
- UI/UX doesn't match approved designs

**Route: QA → Dev (direct)**
- QA provides specific bug report with repro steps
- Dev fixes implementation issues
- Story returns to QA for re-verification

#### Type B: Requirements Mismatch  
- Acceptance criteria were unclear or misinterpreted
- Missing edge cases or scenarios
- Implementation doesn't match PO intent
- Definition of "done" was ambiguous

**Route: QA → PO (for clarification) → Dev (with updated criteria)**
- QA documents specific mismatch with examples
- PO clarifies requirements and updates acceptance criteria
- Updated story with PO sign-off goes back to Dev
- Dev implements against clarified requirements

#### Type C: Fundamentally Broken Story
- Multiple acceptance criteria failures
- Story scope too large or unclear
- Technical approach needs complete rethink
- Dependencies not properly identified

**Route: QA → Three Amigos Session (PO + Dev + QA)**
- Story pulled back to refinement
- Team collaboratively redefines requirements
- New Definition of Ready review required
- Fresh PO sign-off needed before restart

### When PO Rejects from QA

**PO must specify rejection reason:**

#### Business Logic Wrong
- Implementation doesn't deliver expected business value
- User flow doesn't match intended experience
- Acceptance criteria were misunderstood

**Route: PO provides written feedback → Dev fixes → QA re-verifies**

#### Story Was Poorly Defined
- Original acceptance criteria were incomplete
- Technical dependencies missed
- Edge cases not considered

**Route: PO updates story → New Definition of Ready review → Dev implements → QA verifies**

### When Dev Rejects During Planning

#### Technical Blockers
- Dependencies not available
- Infrastructure not ready
- External APIs not accessible

**Route: Dev documents blockers → PO prioritizes resolution → Story returns to backlog until ready**

#### Story Too Large
- Cannot be completed in one sprint
- Multiple technical components involved
- Acceptance criteria cover too much scope

**Route: Dev + PO collaborate to split story → Multiple smaller stories with individual Definition of Ready**

## Notification Channels

### Rejection Notification Routing
- **PO/Process Rejections**: #scrum channel + story ticket documentation
- **Technical/Bug Rejections**: #development channel + story ticket documentation  
- **Escalations**: Both #scrum and #development channels + story ticket documentation

### Communication Rules
- All rejections must be documented in the story ticket using appropriate template
- Channel notification includes story ID and brief summary
- Detailed rejection reasoning stays in the ticket for audit trail

## Rejection Documentation Requirements

### QA Rejection Report Template
```
STORY REJECTED: [Story ID] - [Story Title]
Rejection Type: [Implementation Bug | Requirements Mismatch | Fundamentally Broken]
Date: [YYYY-MM-DD]
QA: Hex

Environment State:
- [Environment details, versions, test data used]
- [System state when failure occurred]

What Broke:
- [Exact behavior observed]
- [Error messages, crashes, unexpected results]

Reproduction Steps:
1. [Step-by-step to reproduce]
2. [Include any preconditions]

Related Acceptance Criteria:
- [Which criteria this affects]
```

### PO Clarification Template  
```
STORY CLARIFICATION: [Story ID] - [Story Title]
Date: [YYYY-MM-DD]
PO: Pearl

Issue Identified:
- [Specific problem with original criteria]

Updated Requirements:
- [Revised acceptance criteria in Given/When/Then format]

Additional Context:
- [Any clarifications for Dev team]

New PO Sign-off: [Date and timestamp]
```

## Escalation Rules

### If Rejection Loops Occur (>2 cycles)
- Mandatory three amigos session
- Story architect review for technical complexity
- Consider splitting story into smaller components

### If PO and QA Disagree on Rejection
- Dev team provides technical input
- Scrum Master facilitates resolution session
- Document agreed resolution for future reference

---

**Implementation Rule**: No story bounces between roles more than twice without escalation. Fix the process, don't repeat the cycle.
