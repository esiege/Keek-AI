# Bug 001: PII/Sensitive Data in Public Repo Files

**Reported**: 2026-02-04  
**Status**: Fixed  
**Severity**: High

## Description
Text files in the repository may contain personal identifiable information (PII) or sensitive data that should not be exposed in a public GitHub repository.

## Expected Behavior
All files in the public repo should contain only generic/placeholder information, no real:
- Email addresses (except generic project emails)
- Personal names
- API keys or tokens
- File paths with usernames
- Discord server IDs or tokens
- Any credentials

## Reproduction Steps
1. Review all markdown and text files in repo
2. Search for patterns: emails, names, paths with usernames, keys

## Environment
- Repo: github.com/esiege/Keek-AI
- Files: docs/, tasks/, bugs/, root files

## Related Story/Task
Interrupts: Sprint 1, Story 2 (Configure Discord)

## Investigation Checklist
- [ ] Search for email patterns
- [ ] Search for Windows user paths
- [ ] Search for API key patterns
- [ ] Search for personal names
- [ ] Review all committed files
