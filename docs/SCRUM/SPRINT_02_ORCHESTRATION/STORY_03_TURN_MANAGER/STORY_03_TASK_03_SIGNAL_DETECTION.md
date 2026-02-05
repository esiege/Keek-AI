# Task: Implement Turn Signal Detection

**Story**: 03 - Build Turn Manager  
**Task ID**: STORY_03_TASK_03  
**Status**: Done

## Objective
Implement detection of TURN_COMPLETE signals and timeout handling.

## Steps

### 1. Signal Detection
- Parse agent responses for TURN_COMPLETE
- Strip signal from displayed message
- Trigger turn advancement

### 2. Timeout Handling
- Track turn start time
- Calculate timeout deadline
- Check for expiry (polling or timer)
- Force turn end on timeout

### 3. Signal Configuration
- Configurable signal keyword
- Configurable timeout per agent
- Optional timeout announcements

### 4. Document Detection Logic
- Regex patterns
- Timing logic
- Configuration options

## Expected Outcome
- TURN_COMPLETE reliably detected
- Timeouts trigger turn advancement
- Configuration documented

## Acceptance Check
- [x] Signal detection regex defined
- [x] Timeout tracking logic clear
- [x] Configuration options documented
- [x] Edge cases handled (no signal, late signal)

## Results (2026-02-04)
Created SIGNAL_DETECTION.md with:
- TURN_COMPLETE regex and stripping logic
- processAgentResponse flow
- Timeout checking and handling
- Configuration options for signal and timeout
- Edge cases: multiple messages, empty after strip, late signal
