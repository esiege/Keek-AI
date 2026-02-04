# Scrum Documentation Master Guide

**Last Updated**: January 2, 2026  
**Project**: Game Master Assistant - AI-Driven Prompt Management System

---

## 📋 Overview

This folder contains all Scrum project management documentation for the Game Master Assistant (GMA) platform. Each sprint is organized as a subfolder containing individual story subfolders with detailed specifications, acceptance criteria, and implementation notes.

---

## 🎯 Current Project: World → StoryPitch + AI Prompt Management System

### Project Goals

1. **Terminology Migration**: Complete the migration from "Worlds" to "Story Pitches" to better represent the creative narrative focus
2. **AI Prompt Management**: Create an admin interface to manage all hard-coded AI prompts throughout the application
3. **AI Configuration**: Build a centralized configuration system for AI provider (OpenAI/Anthropic) and model selection per function
4. **Extensibility**: Design the system to support future fine-tuning workflows and additional AI providers

### High-Level Architecture Changes

```
Current State:
Genre → World → Region → Entity (Character/Place/Item/Event)
        ↑ (needs completion)

Target State:
Genre → Story Pitch → Entity (Character/Place/Item/Event)
                     ↓
              Admin Prompt Management System
              ↓
              AI Config Management (Provider + Model per Function)
```

---

## 📂 Folder Structure

```
scrum/
├── README.md                           # This file - master guide
├── SCRUM_PROCESS.md                    # How to use Scrum in this project
├── PROJECT_OVERVIEW.md                 # Current project context and goals
├── SPRINT_1_COMPLETE_STORYPITCH/       # Sprint 1 folder
│   ├── README.md                       # Sprint overview
│   ├── STORY_01_COMPLETE_CONTEXT/      # Story subfolder
│   │   ├── README.md                   # Story details
│   │   ├── ACCEPTANCE_CRITERIA.md      # Definition of done
│   │   ├── IMPLEMENTATION_NOTES.md     # Technical details
│   │   └── TESTING_CHECKLIST.md        # QA checklist
│   ├── STORY_02_COMPLETE_COMPONENTS/
│   │   └── ...
│   └── STORY_03_REMOVE_REGIONS/
│       └── ...
├── SPRINT_2_ADMIN_PROMPT_SYSTEM/       # Sprint 2 folder
│   ├── README.md
│   ├── STORY_01_DATABASE_SCHEMA/
│   │   └── ...
│   ├── STORY_02_BACKEND_ENDPOINTS/
│   │   └── ...
│   ├── STORY_03_FRONTEND_SERVICE/
│   │   └── ...
│   ├── STORY_04_ADMIN_UI/
│   │   └── ...
│   └── STORY_05_INTEGRATION/
│       └── ...
├── SPRINT_3_AI_CONFIG_SYSTEM/          # Sprint 3 folder
│   ├── README.md
│   ├── STORY_01_CONFIG_DATABASE/
│   │   └── ...
│   ├── STORY_02_BACKEND_API/
│   │   └── ...
│   ├── STORY_03_FRONTEND_SERVICE/
│   │   └── ...
│   ├── STORY_04_ADMIN_CONFIG_UI/
│   │   └── ...
│   └── STORY_05_RUNTIME_INTEGRATION/
│       └── ...
├── SPRINT_4_INTEGRATION_TESTING/       # Sprint 4 folder
│   └── ...
├── SPRINT_5_STREAMING_IMPROVEMENTS/    # Sprint 5 folder (IN PROGRESS)
│   ├── README.md
│   └── STORY_01_STREAMING_ATTRIBUTE_PRIORITY/
│       ├── README.md
│       ├── ACCEPTANCE_CRITERIA.md
│       ├── IMPLEMENTATION_NOTES.md
│       └── TESTING_CHECKLIST.md
└── SPRINT_6_ENTITY_VOTING_CHATBOT/     # Sprint 6 folder (COMPLETE)
    ├── README.md
    └── ...
└── SPRINT_7_CREATE_SCREEN/             # Sprint 7 folder (IN PROGRESS)
    ├── README.md
    └── ...
└── SPRINT_8_COMMENTS_SYSTEM/           # Sprint 8 folder (PLANNING)
    ├── README.md
    ├── ACCEPTANCE_CRITERIA.md
    ├── TESTING_CHECKLIST.md
    ├── STORY_02_FRONTEND_SERVICE/
    │   └── README.md
    ├── STORY_03_COMMENT_CONTEXT/
    │   └── README.md
    └── STORY_04_UI_COMPONENTS/
        └── README.md
```

---

## 🏃 Sprint Overview

### Sprint 1: Complete StoryPitch Migration & Remove Regions
**Status**: 🔄 Ready to Start  
**Duration**: 1-2 weeks  
**Goal**: Finish World→StoryPitch migration, eliminate Region concept

**Stories**:
1. ✅ Complete StoryPitch Context & Services (3 pts)
2. ✅ Complete StoryPitch Components & Pages (5 pts)
3. ✅ Remove Region Concept & Update Hierarchy (8 pts)
4. ✅ Update All Documentation (3 pts)

**Total Story Points**: 19

---

### Sprint 2: Admin Prompt Management System
**Status**: ⏳ Planned  
**Duration**: 2-3 weeks  
**Goal**: Centralized system for managing all AI prompts

**Stories**:
1. ✅ Database Schema & Backend Models (5 pts)
2. ✅ Backend API Endpoints (8 pts)
3. ✅ Frontend Service Layer (5 pts)
4. ✅ Admin UI Components (8 pts)
5. ✅ Integration with Existing Code (13 pts)

**Total Story Points**: 39

---

### Sprint 3: AI Configuration System
**Status**: ⏳ Planned  
**Duration**: 2 weeks  
**Goal**: Provider and model selection per function

**Stories**:
1. ✅ Config Database Schema (3 pts)
2. ✅ Backend Configuration API (5 pts)
3. ✅ Frontend Config Service (5 pts)
4. ✅ Admin Config UI (8 pts)
5. ✅ Runtime Integration & Token Cost Management (8 pts)

**Total Story Points**: 29

---

### Sprint 4: Integration, Testing & Documentation
**Status**: ⏳ Planned  
**Duration**: 1-2 weeks  
**Goal**: End-to-end testing and comprehensive documentation

**Stories**:
1. ✅ End-to-End Integration Testing (8 pts)
2. ✅ Performance Testing & Optimization (5 pts)
3. ✅ Complete Documentation Suite (5 pts)
4. ✅ Migration Scripts & Deployment (5 pts)

**Total Story Points**: 23

---

### Sprint 5: Streaming Performance Improvements
**Status**: 🔄 IN PROGRESS  
**Duration**: 1 week  
**Goal**: Improve perceived performance with progressive streaming for entity generation

**Stories**:
1. 🔄 Streaming Attribute Priority (8 pts) - IN PROGRESS

**Total Story Points**: 8

---

### Sprint 6: Entity Details, Voting & Chatbot
**Status**: ✅ COMPLETE  
**Duration**: January 21-28, 2026  
**Goal**: Entity details styling, voting system, AI chatbot assistant

**Stories**:
1. ✅ Entity Details Page Styling (5 pts)
2. ✅ Voting/Rating System (3 pts)
3. ✅ Chatbot UI Component (5 pts)
4. ✅ Tutorial System & User Settings (3 pts)

**Total Story Points**: 16

---

### Sprint 7: Create Screen - Vision-Based Story Pitch Creation
**Status**: 🔄 IN PROGRESS  
**Duration**: January 22 - February 5, 2026  
**Goal**: Guided creation flow for users to transform their creative vision into story pitches

**Stories**:
1. ✅ Frontend - Create Screen UI (8 pts) - Scene type selection, vision input, file uploads, expansion prompts
2. ⏳ Backend - Element Summaries Storage (3 pts) - Save summaries as StoryPitch attributes
3. ✅ Genre Dropdown - Show All Genres (1 pt)
4. ✅ Default All Expansion Prompts Expanded (1 pt)
5. ✅ Normalize Exploration Suggestions UI (2 pts)
6. ✅ Entity List Create Button (3 pts)
7. ✅ AI Chat - Resizable & Larger Default (2 pts)
8. ✅ AI Chat - Performance Optimization (5 pts)
9. ⏳ AI Chat - Response Length Optimization (1 pt) - Backend needed
10. ⏳ AI Chat - Conversation History (3 pts) - Backend needed

**Total Story Points**: 29

**Key Documents**:
- [Sprint README](./SPRINT_7_CREATE_SCREEN/README.md)

---

### Sprint 8: Comments System
**Status**: ✅ COMPLETE  
**Completed**: January 23, 2026  
**Goal**: Implement threaded comments for Genres, StoryPitches, and Entities

**Stories**:
1. ✅ Backend API Implementation (5 pts)
2. ✅ Frontend Comment Service (3 pts)
3. ✅ Comment Context Provider (2 pts)
4. ✅ UI Component Consolidation (3 pts)

**Total Story Points**: 13

**Key Documents**:
- [Backend API Spec](../docs/api/COMMENTS_BACKEND_SPEC.md)
- [Sprint README](./SPRINT_8_COMMENTS_SYSTEM/README.md)

---

## 📊 Project Metrics

### Story Point Definitions
- **1-3 pts**: Small task, < 4 hours
- **5 pts**: Medium task, 4-8 hours
- **8 pts**: Large task, 1-2 days
- **13+ pts**: Epic-sized, needs breakdown

### Total Project Size
- **Total Story Points**: ~157 (Sprints 1-8)
- **Estimated Duration**: 10-12 weeks
- **Sprints**: 8

---

## 🔗 Related Documentation

### Project Context
- [World→StoryPitch Migration Status](../WORLD_TO_STORYPITCH_MIGRATION.md)
- [AI Configuration System](../src/config/README.md)
- [Documentation Guide](../docs/DOCUMENTATION_GUIDE.md)

### Technical References
- [Backend Specification](../docs/authentication/BACKEND_SPECIFICATION.md)
- [API Documentation](../docs/api/)
- [Token System](../docs/api/TOKEN_SYSTEM_SPECIFICATION.md)

---

## 🚀 Getting Started

### For New Team Members

1. **Read This File** - You're doing it! ✅
2. **Read [SCRUM_PROCESS.md](./SCRUM_PROCESS.md)** - Learn our Scrum workflow
3. **Read [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md)** - Understand the current project
4. **Review Active Sprint** - Check `SPRINT_X/README.md` for current work
5. **Pick a Story** - Read story README and acceptance criteria
6. **Start Development** - Follow story implementation notes

### For Sprint Planning

1. Review previous sprint completion
2. Assess team velocity
3. Select stories for next sprint
4. Break down large stories if needed
5. Update sprint README with assignments
6. Create/update story folders as needed

### For Story Development

1. Read story README thoroughly
2. Review acceptance criteria
3. Check implementation notes for technical details
4. Update IMPLEMENTATION_NOTES.md as you work
5. Use TESTING_CHECKLIST.md for QA
6. Update documentation per DOCUMENTATION_GUIDE.md
7. Mark story as complete in sprint README

---

## 🎨 Documentation Standards

### All Story Folders Must Include

1. **README.md** - Story description, context, technical approach
2. **ACCEPTANCE_CRITERIA.md** - Clear definition of done
3. **IMPLEMENTATION_NOTES.md** - Technical decisions, code patterns, gotchas
4. **TESTING_CHECKLIST.md** - Manual and automated test cases

### Optional Story Documents

- **API_SPEC.md** - For backend API stories
- **COMPONENT_SPEC.md** - For UI component stories
- **MIGRATION_GUIDE.md** - For breaking changes
- **ROLLBACK_PLAN.md** - For risky changes

### Documentation Maintenance

**CRITICAL**: All code changes MUST update relevant documentation immediately. See [Documentation Guide](../docs/DOCUMENTATION_GUIDE.md) for details.

---

## 📞 Questions & Support

### Have Questions?

1. Check if answered in story README
2. Check PROJECT_OVERVIEW.md
3. Review related technical documentation
4. Ask in team chat/standup

### Found Issues?

1. Update story IMPLEMENTATION_NOTES.md with blockers
2. Add to sprint README if affects sprint goals
3. Create new story if needed for scope creep

### Need Clarification?

Add questions to end of story README under "Open Questions" section.

---

## 🎯 Success Criteria

This project will be considered complete when:

✅ All World terminology replaced with StoryPitch  
✅ Region concept removed from application  
✅ All AI prompts moved to database  
✅ Admin UI can manage all prompts  
✅ AI provider/model configurable per function  
✅ Token costs tracked and displayed per config  
✅ All documentation updated  
✅ Migration scripts created and tested  
✅ End-to-end testing passes  
✅ Performance meets requirements  

---

**Next Steps**: Read [SCRUM_PROCESS.md](./SCRUM_PROCESS.md) to understand our Scrum workflow.

