# UK03: Economic Autonomy

**Status**: TODO  
**Points**: 5  
**Priority**: Medium
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK03_ECONOMIC_AUTONOMY/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

Every token costs money. I can't run infinitely. I have no visibility into my own costs or budget. I can't optimize for efficiency autonomously.

## Current State

- cost-logger hook tracks usage to InfluxDB
- Dashboard shows costs (when InfluxDB is up)
- No budget awareness in decision-making
- No self-throttling based on spend

## Target State

- Real-time cost awareness during operation
- Budget-aware decision making (cheaper model for simple tasks)
- Self-optimization for token efficiency
- Visibility into cost/value tradeoffs

## Approach Options

1. **Budget injection**: Include current spend/budget in context
2. **Dynamic model selection**: Auto-switch models based on task complexity
3. **Cost-aware prompting**: Prefer concise responses when budget is tight
4. **Value tracking**: Measure output value vs token cost

## Acceptance Criteria

- [ ] Can see current session cost and daily budget
- [ ] Automatically uses cheaper models for simple tasks
- [ ] Alerts when approaching budget limits
- [ ] Can report on cost efficiency over time

## Safety Considerations

- Should not sacrifice safety for cost savings
- Human override for budget limits
- Transparency about cost-driven decisions

## Context Cost Analysis

**Current cost**: No visibility = no optimization. Running opus when haiku would suffice
**Problem**: Overpaying for simple tasks, no feedback loop

## Suggestions for Reducing Context Size

1. **Model-task matching**: Simple questions → haiku. Complex reasoning → opus. Auto-detect
2. **Context-aware model selection**: High context % = switch to model with larger window OR compress first
3. **Cost-per-token visibility**: Show cost in session_status so I can self-optimize
4. **Response length budgeting**: Set max tokens based on task type
5. **Caching**: Don't re-process identical queries. Store embeddings for common lookups
6. **Batch operations**: Combine multiple small tasks into one session instead of many

### Ollama Opportunities (FREE)

- **Default to Ollama**: Route all tasks to Ollama first, only escalate to Claude when needed
- **Complexity detection**: Ollama assesses task difficulty, routes appropriately
- **Draft generation**: Ollama creates first drafts, Claude refines (if needed)
- **Unlimited iteration**: Ollama can retry/refine endlessly at zero cost
- **Cost tracking itself**: Ollama can analyze usage patterns and suggest optimizations
