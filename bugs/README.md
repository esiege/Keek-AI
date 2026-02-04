# Bugs Directory

This folder tracks bugs discovered during Keek AI development.

## Structure

Each bug gets its own folder:
```
bugs/
├── BUG_001_description/
│   ├── DESCRIPTION.md    # Bug details
│   ├── FIXED.md          # Resolution (when fixed)
│   └── FAILURES.txt      # Failed attempts (if any)
└── ...
```

## Process

See [docs/SCRUM/BUG_TRACKING.md](../docs/SCRUM/BUG_TRACKING.md) for full bug tracking process.

## Quick Reference

1. **New bug**: Create `BUG_NNN_description/DESCRIPTION.md`
2. **Failed fix**: Add attempt to `FAILURES.txt`
3. **Successful fix**: Create `FIXED.md`

## Current Bugs

| ID | Description | Status |
|----|-------------|--------|
| BUG_001 | PII in public repo (local username path) | Fixed |
