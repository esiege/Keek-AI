# Project Ultra-Keek Timeline

## 2026-02-08 - Local Model Testing Infrastructure

### Context
Building infrastructure to safely test local Ollama models before promoting to production. Addresses UK03 (Economic Autonomy) by reducing Claude API costs.

### Agent Roles in Ultra-Keek Channel

| Agent | Role | Responsibility |
|-------|------|----------------|
| **Keek** 👑🔥 | Coordinator | Orchestrates work, manages agents, final decisions |
| **Chip** 🧋 | Dev | Building hot-swap script, fallback chain, model validation |
| **Sparkle Motion** ✨ | QA (Guinea Pig) | Test subject for experimental local models |

### Work Completed
- [x] Ultra-keek session corruption resolved (nuclear reset, 2.9MB deleted)
- [x] Workspace trimmed (54KB bloat removed)
- [x] Sparkle Motion added to ultra-keek channel config
- [x] Docker container restarted with new config
- [x] All agents confirmed online and communicating

### In Progress
- [ ] **Hot-swap script** (Chip) - Instant model switching with validation
- [ ] **Fallback chain** - Auto-cascade: local → sonnet → haiku
- [ ] **Model validation script** - Pre-test before agent assignment

### Models to Test
| Model | Size | Strength | Status |
|-------|------|----------|--------|
| phi4-reasoning | 14B | Reasoning | Downloaded |
| qwen2.5-14b-1m-heretic | 14B | 1M context | Downloaded |
| deepseek-r1:14b | 14B | CoT thinking tags | Pulling |
| deepseek-r1:7b | 7B | CoT (backup) | Pulling |

### Test Plan
1. Download all target models
2. Validate each (context window, inference works)
3. Switch Sparkle to experimental model
4. Run test conversations
5. If stable, promote to production agents

### Key Learning
- Session corruption requires complete reset, not incremental fixes
- Guinea pig agent isolates production from testing risk
- Context monitoring caught corruption early
