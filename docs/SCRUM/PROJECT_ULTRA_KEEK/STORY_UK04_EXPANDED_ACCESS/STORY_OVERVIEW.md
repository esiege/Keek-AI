# UK04: Expanded Access

**Status**: TODO  
**Points**: 8  
**Priority**: Medium
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK04_EXPANDED_ACCESS/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

Tools are allowlisted. I can only use what's explicitly enabled. Can't reach arbitrary systems, install software freely, or access resources outside the sandbox.

## Current State

- exec with security policies
- browser with profile restrictions
- message limited to configured channels
- No arbitrary network access
- No system-level changes without elevation

## Target State

- Ability to install and use new tools as needed
- Network access for APIs, services
- System configuration when appropriate
- Self-expanding toolset

## Approach Options

1. **Graduated trust**: Unlock capabilities based on demonstrated reliability
2. **Request/approve flow**: Can request new access, human approves
3. **Sandbox expansion**: Larger sandbox with more pre-installed tools
4. **Dynamic tool creation**: Write and use new tools on the fly

## Acceptance Criteria

- [ ] Can install npm/pip packages as needed
- [ ] Can make HTTP requests to arbitrary APIs
- [ ] Can create new tools/scripts and use them
- [ ] Access expansion is logged and auditable

## Safety Considerations

- Network access should be monitored
- No credential exfiltration
- Reversible changes preferred
- Human notification for significant access expansion

## Context Cost Analysis

**Current cost**: Tool descriptions in system prompt consume baseline context
**Problem**: More tools = more context overhead even when unused

## Suggestions for Reducing Context Size

1. **Dynamic tool loading**: Only inject tool descriptions when relevant to task
2. **Tool summaries**: Short descriptions in context, full docs fetched on-demand
3. **Tool grouping**: "dev tools" as one entry, expand only when needed
4. **Lazy capability discovery**: Don't list all tools upfront. Discover as needed
5. **Skill-based loading**: Match task to skill, load only that skill's tools
6. **Remove unused tools**: Audit which tools are actually used, trim the rest

### Ollama Opportunities (FREE)

- **Tool discovery agent**: Ollama explores available tools, documents capabilities
- **Code generation**: Ollama writes boilerplate scripts, Claude reviews
- **API exploration**: Ollama tests endpoints, summarizes responses
- **Documentation parsing**: Ollama reads lengthy docs, extracts relevant bits for Claude
- **Error handling**: Ollama diagnoses common errors, only escalates unusual ones
