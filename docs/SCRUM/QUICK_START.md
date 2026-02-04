# Quick Start Guide: Keek AI Studio & Scrum Process

**Last Updated**: February 4, 2026  
**For**: Keek AI Project

---

## 🚀 Get Started in 5 Minutes

### Step 1: Understand the Studio (2 minutes)

**What is Keek AI?** A single-machine, multi-role AI development studio that orchestrates logical personas to build web applications collaboratively.

**Core Principles**:
1. **Files as Truth** - Persistent state lives in files, not chat
2. **Discord as Floor** - Status updates, approvals, error surfacing only
3. **Roles Over Humans** - Producer, PM, Dev, QA, Ops as logical personas
4. **Turn-Based Work** - Simulated parallelism through sequential task handoffs
5. **Clear Boundaries** - Dedicated filesystem, single user account, revocable access

---

### Step 2: Read the Core Docs (2 minutes)

#### Must Read (in order):
1. **AI Studio Initial Setup** - Foundation principles and architecture
2. **[README.md](./README.md)** - Project overview and structure
3. **[SCRUM_PROCESS.md](./SCRUM_PROCESS.md)** - How we work with tasks
4. **[PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md)** - Current project details

#### Reference When Needed:
- Task definitions in `tasks/` directory
- Role prompts in `prompts/` directory
- Build artifacts in `artifacts/` directory

---

### Step 3: Pick Your First Task (1 minute)

**Task Workflow**:
1. Review `tasks/` for available tasks
2. Find one marked "status: planned"
3. Update task status to "in-progress"
4. Read task definition (acceptance criteria, details)
5. Execute the task in your assigned role
6. Update task status to "done" when complete

**By Role**:
- **Producer**: Pick "orchestrate task assignment" tasks
- **PM**: Pick "define specs" or "create acceptance criteria" tasks
- **Dev**: Pick "implement feature" or "build component" tasks
- **QA**: Pick "test feature" or "validate criteria" tasks
- **Ops**: Pick "build" or "deploy" tasks

---

## 📊 Project Status

### Current Sprint: Studio Foundation Complete

✅ **Completed**:
- Git repository connected (github.com/esiege/Keek-AI)
- Filesystem structure established
- Directory READMEs implemented
- VS Code shell integration enabled
- PowerShell profile configured with Git alias

🔄 **Next Phase**:
- Start first web application project
- Use studio workflow to design, build, test, deploy
- Document lessons learned

---

## 📁 Key Directories

**Studio Directories**:
```
<workspace_root>/
├── docs/          ← Project specs and plans
├── prompts/       ← Role definitions and agent prompts
├── tasks/         ← Task definitions and state
├── repos/         ← Future: additional repos
├── artifacts/     ← Build outputs and test reports
├── logs/          ← Runtime and orchestration logs
└── secrets/       ← Credentials (never commit)
├── SPRINT_3_AI_CONFIG_SYSTEM/
│   └── README.md                    ← Sprint 3 overview
└── SPRINT_4_INTEGRATION_TESTING/
    └── README.md                    ← Sprint 4 overview
```

**Story Details**:
```
SPRINT_1_COMPLETE_STORYPITCH/
├── STORY_01_COMPLETE_COMPONENTS/
│   ├── README.md                    ← What to build
│   ├── ACCEPTANCE_CRITERIA.md       ← Definition of done
│   ├── IMPLEMENTATION_NOTES.md      ← How to build it
│   └── TESTING_CHECKLIST.md         ← How to test it
├── STORY_02_REMOVE_REGIONS/
├── STORY_03_UPDATE_ENTITY_CONTEXT/
└── STORY_04_UPDATE_DOCUMENTATION/
```

**Process Documentation**:
```
scrum/
├── README.md                        ← Start here
├── SCRUM_PROCESS.md                 ← How we work
├── PROJECT_OVERVIEW.md              ← What we're building
└── QUICK_START.md                   ← This file
```

---

## 🛠️ Common Tasks

### Starting a New Story

1. **Navigate to story folder**
   ```bash
   cd scrum/SPRINT_X/STORY_XX_NAME/
   ```

2. **Read all documentation**
   - README.md (overview)
   - ACCEPTANCE_CRITERIA.md (definition of done)
   - IMPLEMENTATION_NOTES.md (technical approach)
   - TESTING_CHECKLIST.md (QA requirements)

3. **Update story status**
   - In Sprint README, change status to "IN PROGRESS"
   - Assign to yourself

4. **Start coding**
   - Follow patterns in IMPLEMENTATION_NOTES.md
   - Update notes as you work
   - Commit frequently

5. **Test your work**
   - Use TESTING_CHECKLIST.md
   - Check off each item as you test

6. **Complete the story**
   - Verify all acceptance criteria met
   - Update IMPLEMENTATION_NOTES.md with actuals
   - Mark story as "DONE" in Sprint README

---

### Updating Documentation

**CRITICAL**: Always update docs when making code changes!

See [DOCUMENTATION_GUIDE.md](../docs/DOCUMENTATION_GUIDE.md) for full requirements.

**Quick checklist**:
- [ ] Updated inline code comments
- [ ] Updated related specification files
- [ ] Updated code examples
- [ ] Updated "Last Updated" dates
- [ ] Verified cross-references

---

### Running the Project

**Development**:
```bash
npm run dev          # Start dev server (port 8080)
npm run b            # Build + dev (quick iteration)
```

**Building**:
```bash
npm run build        # Production build
npm run build:dev    # Development build
```

**Testing** (manual):
1. Navigate to localhost:8080
2. Follow test scenarios in TESTING_CHECKLIST.md
3. Check console for errors

---

## ❓ FAQ

### Q: Where do I start?
**A**: Read this file, then [README.md](./README.md), then [SPRINT_1_COMPLETE_STORYPITCH/README.md](./SPRINT_1_COMPLETE_STORYPITCH/README.md)

### Q: Can I skip the documentation?
**A**: No! Documentation is mandatory. See [DOCUMENTATION_GUIDE.md](../docs/DOCUMENTATION_GUIDE.md)

### Q: What if a story takes longer than estimated?
**A**: That's normal! Update the sprint README with progress notes. Consider breaking the story into smaller pieces.

### Q: Can I work on multiple stories at once?
**A**: Not recommended. Focus on one story at a time. Mark it DONE before starting the next.

### Q: What if I find a bug while working on a story?
**A**: Document it in IMPLEMENTATION_NOTES.md under "Known Issues". Fix if critical, otherwise add to backlog.

### Q: How do I know if I'm done with a story?
**A**: All checkboxes in ACCEPTANCE_CRITERIA.md are checked, all tests in TESTING_CHECKLIST.md pass, and all docs are updated.

### Q: What if I have questions about a story?
**A**: Add them to the "Open Questions" section in the story README. Discuss in standup or team chat.

### Q: Can I change the story scope?
**A**: Small adjustments are okay. Major changes should be discussed and may need a new story.

### Q: How do I handle blockers?
**A**: Document in IMPLEMENTATION_NOTES.md, update sprint README, escalate if critical, work on alternative story if possible.

---

## 🎓 Learning Resources

### Internal Documentation
- [Architecture Overview](../docs/README.md)
- [Authentication Guide](../docs/authentication/)
- [API Specifications](../docs/api/)
- [Token System](../docs/api/TOKEN_SYSTEM_SPECIFICATION.md)

### External Resources
- [React Documentation](https://react.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [shadcn/ui Components](https://ui.shadcn.com/)
- [Azure Functions Docs](https://learn.microsoft.com/en-us/azure/azure-functions/)

---

## 🚨 Important Reminders

### Before You Code
1. ✅ Read all story documentation
2. ✅ Understand acceptance criteria
3. ✅ Review existing code patterns
4. ✅ Check for dependencies

### While You Code
1. ✅ Follow existing patterns
2. ✅ Write clean, documented code
3. ✅ Commit frequently
4. ✅ Update IMPLEMENTATION_NOTES.md

### After You Code
1. ✅ Test against TESTING_CHECKLIST.md
2. ✅ Update all relevant documentation
3. ✅ Mark story as DONE
4. ✅ Verify no console errors

### Never Forget
1. 🔥 **ALWAYS UPDATE DOCUMENTATION**
2. 🔥 **TEST THOROUGHLY**
3. 🔥 **FOLLOW ESTABLISHED PATTERNS**
4. 🔥 **COMMIT OFTEN WITH GOOD MESSAGES**

---

## 📞 Need Help?

### Stuck on Something?

1. **Check documentation first**
   - Story README
   - Implementation notes
   - Project overview
   - Related code

2. **Search codebase**
   - Look for similar patterns
   - Check existing components
   - Review service implementations

3. **Ask for help**
   - Document your question in story README
   - Discuss in standup
   - Ask in team chat
   - Review with another developer

---

## 🎉 Ready to Go!

You now have everything you need to start working on the Game Master Assistant project!

### Your Next Steps:

1. **Read [README.md](./README.md)** if you haven't
2. **Review [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md)** for context
3. **Go to [SPRINT_1_COMPLETE_STORYPITCH/](./SPRINT_1_COMPLETE_STORYPITCH/)** to start
4. **Pick [STORY_01](./SPRINT_1_COMPLETE_STORYPITCH/STORY_01_COMPLETE_COMPONENTS/)** as your first task
5. **Start coding!** 🚀

---

## 📊 At a Glance

### Project Stats
- **Total Sprints**: 4
- **Total Story Points**: 110
- **Estimated Duration**: 7-9 weeks
- **Total Stories**: 16

### Sprint Breakdown
- Sprint 1: 4 stories, 19 points
- Sprint 2: 5 stories, 39 points
- Sprint 3: 5 stories, 29 points
- Sprint 4: 4 stories, 23 points

### Key Deliverables
- ✅ StoryPitch architecture
- ✅ Prompt management system
- ✅ AI configuration system
- ✅ Admin UI for both systems
- ✅ Complete documentation
- ✅ Production deployment

---

**Good luck! Build something amazing! 🎮✨**

