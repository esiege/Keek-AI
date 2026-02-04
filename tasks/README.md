# tasks/

**Note**: Tasks are now nested within story folders in `docs/SCRUM/`

## Task Organization

Tasks are granular subtasks of stories. Each task is a `.md` file within a story folder:

```
docs/SCRUM/
└── SPRINT_1_WEB_APP_FOUNDATION/
    └── STORY_01_PROJECT_SETUP/
        ├── README.md                    # Story overview
        ├── ACCEPTANCE_CRITERIA.md       # Definition of done
        ├── IMPLEMENTATION_NOTES.md      # Technical approach
        ├── TESTING_CHECKLIST.md         # QA steps
        ├── STORY_01_TASK_01.md          # Individual task
        ├── STORY_01_TASK_02.md          # Individual task
        └── STORY_01_TASK_03.md          # Individual task
```

Each task file contains:
- **Objective**: What needs to be accomplished
- **Acceptance Criteria**: How to verify it's done
- **Implementation Notes**: Approach and technical details
- **Dependencies**: Must complete X first
- **Points**: Story point estimate

## This Directory

Previously designed for global task state tracking. With the sprint/story/task hierarchy, all task definitions now live within story folders in `docs/SCRUM/`. This folder can be used for quick task reference during sprint execution if needed, but the source of truth is in story folders.
