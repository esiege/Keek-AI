# Scrum Process Guide

**Last Updated**: February 4, 2026  
**Version**: 1.0
**For**: Keek AI Multi-Role Development Studio

---

## 📋 Overview

This document explains how to use Scrum for the Keek AI project. Keek AI is a single-machine studio with multiple logical roles (Producer, PM, Dev, QA, Ops) that coordinate through task-based handoffs and file-based state management.

**Core Principle**: Tasks are the primary coordination mechanism. Each task moves through states (planned → in-progress → done) with a single owner at a time.

---

## 🔄 Scrum Framework

### Core Principles

1. **Task-Based Coordination** - Work moves through explicit task objects with clear ownership
2. **File-Based State** - All state lives in files (`tasks/`), not chat  
3. **Role Clarity** - Each person adopts a logical role with defined responsibilities
4. **Explicit Handoffs** - One task owner at a time; handoff documented when role changes
5. **Transparent Progress** - Discord updates on blockers and completions
6. **Documentation First** - Write acceptance criteria before implementing

---

## 🏃 Sprint Cycle

A sprint is a 1-2 week planning period containing multiple stories, each with tasks.

### Sprint Structure

```
Sprint 1: Web App Foundation (2 weeks)
├── Story 1: Project Setup (5 points)
│   ├── Task 1: Initialize repo and build system
│   ├── Task 2: Setup CI/CD pipeline
│   └── Task 3: Configure dev environment
├── Story 2: Core Components (8 points)
│   ├── Task 1: Build UI framework
│   ├── Task 2: Implement state management
│   ├── Task 3: Create API client
│   └── Task 4: Setup testing framework
└── Story 3: Testing & Deployment (5 points)
    ├── Task 1: Write test suite
    ├── Task 2: Setup staging environment
    └── Task 3: Deploy to production
```

### Sprint Phases

#### 1. Sprint Planning (1-2 hours)
**Goal**: Define stories and plan the sprint

**Activities**:
- Review project backlog
- Estimate story points (using Fibonacci: 1, 2, 3, 5, 8, 13)
- Assign stories to sprint
- Break each story into tasks
- Create story folders and task files
- Set acceptance criteria for each story

**Output**:
- Sprint folder created with README
- Story folders created with acceptance criteria
- Task files defined (STORY_NN_TASK_MM.md)
- Sprint goal documented

#### 2. Sprint Execution (1-2 weeks)
**Goal**: Complete stories through their tasks

**Daily Activities**:
- Pick highest priority incomplete task
- Read task description and dependencies
- Implement per task specification
- Test against story's acceptance criteria
- Update task status and log progress
- Commit with story/task reference

**Story Workflow**:
```
1. Story in PLANNED status
   ↓
2. Pick highest priority uncompleted task
   ↓
3. Read STORY_XX_TASK_MM.md
   ↓
4. Implement feature (following IMPLEMENTATION_NOTES.md)
   ↓
5. Test against TESTING_CHECKLIST.md
   ↓
6. If all tasks done → Story DONE
   ↓
7. Move to next story
```

#### 3. Sprint Review (30-60 minutes)
**Goal**: Demonstrate completed stories and gather feedback

**Activities**:
- Demo each completed story
- Verify acceptance criteria met
- Get stakeholder feedback
- Document lessons learned
- Calculate velocity (story points completed / 2 weeks)
- Plan next sprint improvements

**Output**:
- Sprint completion summary in sprint README
- Velocity metrics for next planning
```
1. Story TODO → IN PROGRESS
2. Implement features
3. Run tests
4. Update docs
5. Code review (if team)
6. Story IN PROGRESS → DONE
7. Move to next story
```

**Tools**:
- Use GitHub issues/projects (optional)
- Update sprint README with progress
- Track blockers in IMPLEMENTATION_NOTES.md

---

#### 3. Sprint Review (30-60 minutes)
**Goal**: Demonstrate completed work

**Activities**:
- Demo each completed story
- Review acceptance criteria met
- Get feedback (from team or stakeholders)
- Document lessons learned
- Update velocity metrics

**Output**:
- Sprint completion summary in sprint README
- Velocity calculation for next sprint
- Feedback notes

---

#### 4. Sprint Retrospective (30 minutes)
**Goal**: Improve the process

**Questions**:
- What went well?
- What could be improved?
- What will we change next sprint?
- Were story points accurate?
- Did we meet sprint goal?

**Output**:
- RETROSPECTIVE.md in sprint folder
- Action items for next sprint
- Process improvements documented

---

## 📁 Folder Structure Guidelines

### Sprint Folder Naming
```
SPRINT_X_SHORT_DESCRIPTION/
```

**Examples**:
- `SPRINT_1_COMPLETE_STORYPITCH/`
- `SPRINT_2_ADMIN_PROMPT_SYSTEM/`
- `SPRINT_3_AI_CONFIG_SYSTEM/`

### Story Folder Naming
```
STORY_XX_SHORT_DESCRIPTION/
```

**Examples**:
- `STORY_01_COMPLETE_CONTEXT/`
- `STORY_02_DATABASE_SCHEMA/`
- `STORY_03_ADMIN_UI/`

---

## 📝 Story Structure

### Required Files

#### 1. README.md
**Purpose**: Story overview and context

**Sections**:
```markdown
# Story Title

**Story ID**: STORY_XX
**Story Points**: X
**Status**: TODO | IN PROGRESS | DONE
**Assigned To**: [Name]
**Sprint**: Sprint X

## User Story
As a [role], I want [feature] so that [benefit].

## Description
Detailed explanation of what needs to be built.

## Technical Approach
High-level overview of how to implement.

## Dependencies
- Related stories
- Required before starting
- Blocks other stories

## Definition of Done
- [ ] Checklist item 1
- [ ] Checklist item 2

## Open Questions
- Question 1?
- Question 2?
```

---

#### 2. ACCEPTANCE_CRITERIA.md
**Purpose**: Clear definition of "done"

**Format**:
```markdown
# Acceptance Criteria

## Functional Requirements
**GIVEN** [context]
**WHEN** [action]
**THEN** [expected outcome]

## Non-Functional Requirements
- Performance criteria
- Security requirements
- Accessibility standards

## Testing Requirements
- Unit tests
- Integration tests
- Manual test cases

## Documentation Requirements
- Code comments
- API docs
- User guides
```

---

#### 3. IMPLEMENTATION_NOTES.md
**Purpose**: Technical details and decisions

**Sections**:
```markdown
# Implementation Notes

## Technical Decisions
- Why approach X was chosen
- Alternatives considered
- Tradeoffs made

## Code Patterns
- Patterns to follow
- Examples from codebase
- Anti-patterns to avoid

## Key Files Modified
- File path: What changed
- File path: What changed

## Gotchas & Edge Cases
- Tricky scenarios
- Known issues
- Workarounds

## Performance Considerations
- Bottlenecks
- Optimization opportunities

## Security Considerations
- Auth requirements
- Data validation
- Injection prevention

## Blockers
- [Date] Description of blocker
- [Date] How blocker was resolved
```

---

#### 4. TESTING_CHECKLIST.md
**Purpose**: QA and validation

**Format**:
```markdown
# Testing Checklist

## Unit Tests
- [ ] Test case 1
- [ ] Test case 2

## Integration Tests
- [ ] Integration scenario 1
- [ ] Integration scenario 2

## Manual Testing
### Happy Path
- [ ] Test step 1
- [ ] Test step 2

### Error Cases
- [ ] Error scenario 1
- [ ] Error scenario 2

### Edge Cases
- [ ] Edge case 1
- [ ] Edge case 2

## Regression Testing
- [ ] Old feature still works
- [ ] No performance degradation

## Browser/Device Testing
- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Mobile responsive

## Accessibility Testing
- [ ] Screen reader compatible
- [ ] Keyboard navigation
- [ ] Color contrast

## Documentation Testing
- [ ] Code examples work
- [ ] API docs accurate
- [ ] Links valid
```

---

## 📊 Story Points

### Point Scale (Fibonacci)
- **1 point**: Trivial, < 1 hour
- **2 points**: Simple, 1-2 hours
- **3 points**: Small, 2-4 hours
- **5 points**: Medium, 4-8 hours (half day to full day)
- **8 points**: Large, 1-2 days
- **13 points**: Very large, 2-3 days
- **21+ points**: Epic - must be broken down

### Estimation Guidelines

**Consider**:
- Development time
- Testing time
- Documentation time
- Learning curve
- Uncertainty/risk

**Tips**:
- Use past stories as reference
- Round up if uncertain
- Break down large stories
- Re-estimate if blocked

---

## 🎯 Definition of Done

A story is "Done" when:

### Code Complete
- ✅ All acceptance criteria met
- ✅ Code follows project conventions
- ✅ No console errors or warnings
- ✅ Edge cases handled
- ✅ Error handling implemented

### Testing Complete
- ✅ All test checklist items pass
- ✅ Unit tests written (if applicable)
- ✅ Integration tests pass
- ✅ Manual testing complete
- ✅ No regressions introduced

### Documentation Complete
- ✅ Code comments added (JSDoc)
- ✅ Implementation notes updated
- ✅ Related docs updated (per DOCUMENTATION_GUIDE.md)
- ✅ API docs updated (if applicable)
- ✅ User-facing docs updated (if applicable)

### Review Complete
- ✅ Code review passed (if team)
- ✅ Design review passed (if UI changes)
- ✅ Security review passed (if security-sensitive)

### Merged & Deployed
- ✅ Code committed with good commit message
- ✅ Merged to main branch
- ✅ Deployed to dev/staging environment
- ✅ Smoke test in deployed environment

---

## 🚦 Story Status

### Status Definitions

**TODO**
- Story planned but not started
- Prerequisites not met
- Blocked by dependencies

**IN PROGRESS**
- Actively being worked on
- One person assigned
- Implementation underway

**BLOCKED**
- Cannot proceed
- External dependency needed
- Decision required

**IN REVIEW**
- Code review requested
- Testing in progress
- Documentation review

**DONE**
- All acceptance criteria met
- All testing complete
- Documentation updated
- Merged and deployed

**CANCELLED**
- Story no longer needed
- Requirements changed
- Replaced by different approach

---

## 🔄 Sprint Workflow

### Week 1: Sprint Start

**Monday** (Sprint Planning):
```
1. Review last sprint completion
2. Calculate velocity
3. Select stories for sprint
4. Create sprint folder
5. Set sprint goal
6. Assign stories
```

**Tuesday-Friday** (Development):
```
1. Daily: Update story status
2. Work on highest priority story
3. Update implementation notes
4. Commit changes frequently
5. Update documentation
```

---

### Week 2: Sprint End

**Monday-Thursday** (Development):
```
1. Complete remaining stories
2. Focus on finishing over starting new
3. Test thoroughly
4. Update all documentation
```

**Friday** (Review & Retrospective):
```
1. Demo completed stories
2. Review acceptance criteria
3. Update sprint README
4. Hold retrospective
5. Plan next sprint
```

---

## 📈 Velocity Tracking

### Calculate Velocity

**Formula**:
```
Velocity = Total Story Points Completed / Sprint Duration
```

**Example**:
- Sprint 1: 15 points completed in 2 weeks = 7.5 points/week
- Sprint 2: 20 points completed in 2 weeks = 10 points/week
- Average velocity: 8.75 points/week

### Use Velocity for Planning

**Next Sprint Capacity**:
```
Capacity = Average Velocity × Sprint Weeks
```

**Example**:
- Average velocity: 8.75 points/week
- Sprint duration: 2 weeks
- Sprint capacity: 17.5 points
- Select stories totaling ~17 points

---

## 🛠️ Tools & Techniques

### Recommended Tools

**Project Management**:
- GitHub Projects (integrated with repos)
- Trello (visual kanban boards)
- Notion (all-in-one workspace)
- Physical board (for tactile preference)

**Documentation**:
- VS Code (for markdown editing)
- Markdown preview extensions
- Mermaid for diagrams
- PlantUML for complex diagrams

**Time Tracking**:
- Toggl Track
- Clockify
- VS Code time tracking extensions

### Best Practices

**Daily Routine**:
1. Review sprint goal
2. Check current story status
3. Work in small commits
4. Update docs as you code
5. Test frequently
6. Push changes at end of day

**Weekly Routine**:
1. Review sprint progress
2. Adjust priorities if needed
3. Update velocity metrics
4. Communicate blockers
5. Plan next week's focus

**Documentation Routine**:
1. Write code → Write docs
2. Make change → Update related docs
3. Finish story → Review all docs
4. End sprint → Comprehensive doc review

---

## 🎓 Learning from Sprints

### Metrics to Track

**Velocity**:
- Points completed per sprint
- Trend over time
- Consistency

**Quality**:
- Bugs found post-deployment
- Stories requiring rework
- Test coverage

**Estimation Accuracy**:
- Actual vs estimated time
- Stories over/under estimated
- Patterns in estimates

**Documentation**:
- Docs updated on time?
- Docs accurate?
- Docs helpful for others?

### Retrospective Questions

**What Went Well?**
- Fast implementation
- Good collaboration
- Clear requirements
- Effective tools

**What Could Improve?**
- Slow tests
- Unclear requirements
- Missing documentation
- Tool limitations

**Action Items**:
- Specific, measurable
- Assigned owner
- Due date
- Success criteria

---

## 🚀 Quick Start Checklist

Starting a new sprint? Use this checklist:

### Sprint Planning
- [ ] Review last sprint retrospective
- [ ] Calculate team velocity
- [ ] Select stories (match capacity)
- [ ] Create sprint folder
- [ ] Write sprint README
- [ ] Set sprint goal
- [ ] Create story folders
- [ ] Write acceptance criteria
- [ ] Assign stories

### During Sprint
- [ ] Update story status daily
- [ ] Work on one story at a time
- [ ] Test continuously
- [ ] Document as you code
- [ ] Commit frequently
- [ ] Review PRs promptly
- [ ] Communicate blockers

### Sprint End
- [ ] Complete all testing
- [ ] Update all documentation
- [ ] Demo completed work
- [ ] Hold retrospective
- [ ] Calculate velocity
- [ ] Update project metrics
- [ ] Plan next sprint

---

## 📞 Getting Help

### Common Issues

**Issue**: Stories taking longer than estimated  
**Solution**: Break down into smaller stories, improve estimation

**Issue**: Unclear requirements  
**Solution**: Add more detail to story README, include examples

**Issue**: Blocked by external dependency  
**Solution**: Mark as BLOCKED, escalate, work on alternative story

**Issue**: Documentation falling behind  
**Solution**: Update docs immediately after code changes, not at sprint end

**Issue**: Too much work in progress  
**Solution**: Limit WIP to 1-2 stories per person, finish before starting new

---

## 🔗 Related Documentation

- [Master Scrum README](./README.md) - Project overview
- [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md) - Current project details
- [Documentation Guide](../docs/DOCUMENTATION_GUIDE.md) - Documentation standards
- Individual sprint READMEs - Sprint-specific information

---

**Remember**: Scrum is flexible. Adapt this process to fit your team's needs. The goal is to deliver value incrementally while maintaining quality and documentation.

