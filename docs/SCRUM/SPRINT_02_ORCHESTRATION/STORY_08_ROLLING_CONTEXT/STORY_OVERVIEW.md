# Story 08: Rolling Context Management

## Summary
Agents maintain a rolling context window at ~50% of the model's limit, automatically summarizing older messages to stay within budget while preserving key information.

## Problem
- Agent conversations can grow unbounded
- Hitting context limits mid-task causes failures or degraded responses
- Manual context management is error-prone

## Solution
Each agent monitors its context size and triggers automatic summarization when approaching the 50% threshold. Recent messages stay verbatim; older content gets compressed into a summary that preserves decisions, actions, and key context.

## Points: 5

## Status: TODO

## Dependencies
- None (standalone enhancement)

## Notes
- 50% target leaves room for agent response + tool outputs
- Summary should be good enough that agent can still answer questions about summarized content
- Could integrate with OpenClaw's built-in compaction if available
