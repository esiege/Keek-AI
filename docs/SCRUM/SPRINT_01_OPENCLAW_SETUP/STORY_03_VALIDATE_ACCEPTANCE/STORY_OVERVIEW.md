# Story 03: Validate Acceptance Criteria

## Story
As a Keek AI studio operator, I want to verify OpenClaw can execute system commands so that it can actually "do things" on this machine.

## Acceptance Criteria
- [ ] OpenClaw has shell/system access enabled
- [ ] Send Discord message requesting to open MS Paint
- [ ] MS Paint opens on this Windows machine

## Story Points: 1

## Tasks

| ID | Task | Status |
|----|------|--------|
| T01 | Enable Shell/System Access | Not Started |
| T02 | Test MS Paint Command | Not Started |

## Technical Notes
- OpenClaw has a "bash" integration for shell commands
- Windows equivalent: PowerShell or cmd execution
- May need to configure permissions/sandbox settings

## Dependencies
- Story 01: OpenClaw installed
- Story 02: Discord integration working

## Sprint Acceptance Test
This story IS the sprint acceptance test. When complete:
> **PASS**: Message OpenClaw on Discord → MS Paint opens

## Safety Considerations
- System access is powerful - understand what you're enabling
- Can be sandboxed per OpenClaw docs
- Review commands before broad access is granted
