# Bug Tracking Process

**For**: Keek AI Multi-Role Development Studio  
**Purpose**: Track and resolve bugs discovered during development

---

## Overview

Bugs are tracked in the `bugs/` folder at the workspace root. Each bug gets its own subfolder with documentation of the issue and resolution attempts.

---

## Bug Folder Structure

```
bugs/
├── BUG_001_short_description/
│   ├── DESCRIPTION.md      # Bug details, reproduction steps
│   ├── FIXED.md            # Created when bug is resolved (optional)
│   └── FAILURES.txt        # Log of failed fix attempts (if any)
├── BUG_002_another_issue/
│   ├── DESCRIPTION.md
│   └── FAILURES.txt
└── ...
```

---

## When to Create a Bug Ticket

### Interruption Rule
**When the user interrupts an in-progress story to report an issue**, immediately:

1. Acknowledge: "Adding a new bug ticket for this issue"
2. Create bug folder: `bugs/BUG_NNN_short_description/`
3. Create `DESCRIPTION.md` with issue details
4. Continue with bug investigation OR return to original task (user's choice)

### Other Triggers
- Test failure during task execution
- Unexpected behavior discovered
- Regression from previous work

---

## Bug Lifecycle

```
1. Bug Discovered
   ↓
2. Create bugs/BUG_NNN_description/DESCRIPTION.md
   ↓
3. Attempt Fix
   ↓
4a. Fix Succeeds → Create FIXED.md with solution
4b. Fix Fails → Log attempt in FAILURES.txt → Retry or Defer
   ↓
5. Bug Resolved (FIXED.md exists)
```

---

## File Templates

### DESCRIPTION.md
```markdown
# Bug NNN: Short Title

**Reported**: YYYY-MM-DD  
**Status**: Open | In Progress | Fixed | Deferred  
**Severity**: Critical | High | Medium | Low

## Description
What is happening that shouldn't be happening?

## Expected Behavior
What should happen instead?

## Reproduction Steps
1. Step one
2. Step two
3. Observe issue

## Environment
- OS: Windows 11
- Related components: [list]

## Related Story/Task
Sprint X, Story Y, Task Z (if applicable)
```

### FIXED.md
```markdown
# Resolution

**Fixed**: YYYY-MM-DD

## Solution
Brief description of what fixed the issue.

## Changes Made
- File/config changes
- Commands run
- etc.

## Verification
How was the fix verified?
```

### FAILURES.txt
```
=== Attempt 1: YYYY-MM-DD ===
What was tried:
[description]

Why it failed:
[reason]

Do not retry this approach.

=== Attempt 2: YYYY-MM-DD ===
...
```

---

## Important Rules

1. **Never retry a failed approach** - Check FAILURES.txt before attempting any fix
2. **Document immediately** - Create bug ticket as soon as issue is reported
3. **Log all failures** - Every failed attempt goes in FAILURES.txt with explanation
4. **Create FIXED.md only when verified** - Don't mark fixed until confirmed working

---

## Bug Numbering

Bugs are numbered sequentially: BUG_001, BUG_002, etc.

To find the next number:
```powershell
Get-ChildItem bugs -Directory | Sort-Object Name | Select-Object -Last 1
```

---

## Integration with Sprints

- Bugs discovered during a sprint may be fixed immediately (if quick) or added to backlog
- Critical bugs interrupt current work
- Non-critical bugs can be scheduled for future sprints
- Reference bug ID in commit messages: `fix(BUG_001): description`
