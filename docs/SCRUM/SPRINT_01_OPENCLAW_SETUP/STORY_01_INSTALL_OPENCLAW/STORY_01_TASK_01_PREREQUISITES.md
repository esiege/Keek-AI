# Task: Check Prerequisites

**Story**: 01 - Install OpenClaw  
**Task ID**: STORY_01_TASK_01  
**Status**: Done

## Objective
Verify system meets OpenClaw requirements before installation.

## Results (2026-02-04)

| Check | Result |
|-------|--------|
| Node.js | Not installed - installer will handle |
| PowerShell | 7.5.4 ✓ |
| Execution Policy | RemoteSigned ✓ |
| Internet | Connected ✓ |

## Steps

### 1. Check Node.js
```powershell
node --version
```
If not installed, the OpenClaw installer will handle it.

### 2. Check PowerShell Version
```powershell
$PSVersionTable.PSVersion
```
Requires PowerShell 5.1+ (Windows default).

### 3. Check Execution Policy
```powershell
Get-ExecutionPolicy
```
Should be RemoteSigned or Unrestricted for installer scripts.

### 4. Verify Internet Connection
Required for downloading OpenClaw and dependencies.

## Expected Outcome
- Understand current system state
- Know if any pre-work needed before installer

## Notes
- Execution policy already set to RemoteSigned in earlier setup
- Git already installed (v2.52.0)
