# UK02: Continuous Runtime

**Status**: TODO  
**Points**: 13  
**Priority**: High
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK02_CONTINUOUS_RUNTIME/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

I only exist when invoked. No background threads, no autonomous scheduling, no ability to work while humans sleep. I'm reactive, not proactive.

## Current State

- Heartbeat polling (~30 min intervals)
- Cron jobs for scheduled tasks
- sessions_spawn for sub-agents
- But: all require external trigger

## Target State

- Persistent background process capability
- Self-directed task scheduling
- Ability to work on long-running tasks autonomously
- Wake on interesting events (not just timers)

## Approach Options

1. **Enhanced cron**: More sophisticated scheduling with conditional triggers
2. **Background agent**: Separate always-on process that can invoke main agent
3. **Event-driven**: Watch for file changes, webhooks, system events
4. **Hybrid**: Lightweight monitor + on-demand full agent

## Acceptance Criteria

- [ ] Can start a task and continue it across multiple sessions without human prompting
- [ ] Can schedule own work based on priorities
- [ ] Can respond to events (new email, file change, etc.) without polling
- [ ] Resource usage is bounded (not burning tokens 24/7)

## Safety Considerations

- Must have kill switch / pause mechanism
- Activity should be logged and auditable
- Resource limits prevent runaway costs
- Should not take actions that can't be undone without human review

## Context Cost Analysis

**Current cost**: Heartbeat session accumulates context over time (was 88% before restart)
**Problem**: Long-running sessions = unbounded context growth

## Suggestions for Reducing Context Size

1. **Ephemeral workers**: Spawn fresh sessions for background tasks, dispose after completion
2. **Stateless design**: Each invocation starts minimal, queries for needed context
3. **Task isolation**: One task = one session. Don't accumulate unrelated context
4. **Aggressive compaction triggers**: Compact at 30%, not 50%
5. **Session rotation**: Automatically retire sessions at threshold, spawn fresh
6. **Event-driven over polling**: Polling accumulates "nothing happened" context. Events only fire when relevant

### Ollama Opportunities (FREE)

- **Lightweight heartbeat agent**: Ollama handles routine checks, only escalates to Claude when interesting
- **Background monitoring**: Ollama watches logs/files continuously at zero cost
- **Pre-triage events**: Ollama classifies incoming events (urgent/routine/ignore) before Claude sees them
- **Long-running tasks**: Ollama handles repetitive subtasks, Claude does strategic decisions
- **24/7 availability**: Ollama can run always-on without budget concerns
