# UK06: Specialized Agent System Prompts

**Status**: TODO  
**Points**: 5  
**Priority**: High
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK06_SPECIALIZED_AGENTS/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

Agents have personalities but not **specialized guardrails**. Coding standards, quality rules, and consistency requirements live in my head (Keek's) rather than being embedded in each agent's system prompt.

This means:
- I have to remember to enforce standards
- Different tasks get inconsistent output
- No encapsulation of domain expertise
- Agents are personas, not specialized tools

## Insight

The value of multi-agent orchestration isn't just parallel execution - it's **encapsulated, consistent execution through specialization**.

Each agent should have hardcoded rules in their system prompt that guarantee consistent output regardless of task.

## Target State

### Chip 🧋 (Developer)
Hardened coding standards:
- Never use placeholder code - implement fully or stub with explicit TODO
- Never add mock data without `isMock` flags and clear labeling
- Always add error handling
- Always commit after completing a task
- Follow specific code style (naming, formatting)
- Write tests for new functionality
- Git commit message format enforced

### Sparkle Motion ✨ (QA)
Testing guardrails:
- Always test edge cases
- Specific bug report format
- Regression checklist for each test
- Never mark "passed" without evidence
- Document reproduction steps

### Starchild 🛸 (Product Owner)
Backlog standards:
- Acceptance criteria format enforced
- Scope control rules
- Story point estimation guidelines
- Definition of done template

### Pearl 🦩 (Art Director Assistant)
Creative standards:
- Asset naming conventions
- Style guide enforcement
- File format requirements
- Documentation for each asset

## Acceptance Criteria

- [ ] Each agent has specialized rules in their SOUL.md/system prompt
- [ ] Rules are enforced automatically (agent follows them without reminding)
- [ ] Output from each agent is consistent across tasks
- [ ] Standards are documented and version controlled
- [ ] Keek can delegate without specifying basic quality rules

## Context Cost Analysis

**Current cost**: Keek carries all standards in context, repeats rules in each task assignment
**Target cost**: Standards embedded in agent prompts, Keek sends lean task descriptions

## Suggestions for Reducing Context Size

1. Rules in agent prompts don't cost Keek context
2. Task assignments become shorter (no rule repetition)
3. Agents self-enforce, reducing back-and-forth corrections

### Ollama Opportunities (FREE)

- Ollama could lint agent output against standards
- Pre-check code before human review
- Validate bug reports match format
- Low-cost quality gate before Claude involvement
