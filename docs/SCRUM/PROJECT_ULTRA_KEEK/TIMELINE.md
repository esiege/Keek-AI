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

### Key Learnings
- Session corruption requires complete reset, not incremental fixes
- Guinea pig agent isolates production from testing risk
- Context monitoring caught corruption early

### Local Model Testing Results (2026-02-08)

**Tested Models:**
| Model | Result | Issue |
|-------|--------|-------|
| qwen2.5-14b-1m-heretic | ❌ Failed | No tool support |
| mistral:7b | ❌ Failed | Too slow (CPU only), model confusion |

**Root Causes:**
1. **No GPU** - NucBox runs 100% CPU inference, too slow for real-time
2. **Model intelligence** - 7B models can't follow complex agent instructions
3. **Tool support** - Reasoning models strip tool calling capability

**Conclusion:**
Local LLMs on CPU-only hardware = background jobs only
Real-time agents require cloud models or GPU acceleration

**Recommended Hybrid Approach:**
- Local models: summarization, memory compaction, batch processing
- Cloud models: interactive agents, real-time chat, complex reasoning

---

## 2026-02-08 02:05 - Remote Ollama Server SUCCESS

### Breakthrough
Configured Jesse's main PC (RTX 3090 24GB) as remote Ollama inference server. NucBox agents connect over LAN for GPU-accelerated local inference.

### Architecture
```
┌─────────────────┐         ┌─────────────────┐
│    NucBox       │   LAN   │  Jesse's PC     │
│  (Orchestration)│◄───────►│  (Inference)    │
│                 │         │                 │
│ - OpenClaw GW   │  :11434 │ - RTX 3090 24GB │
│ - Docker agents │         │ - Ollama server │
│ - Agent configs │         │ - D:\models     │
└─────────────────┘         └─────────────────┘
```

### Connection Details
- **Server IP**: 192.168.0.220
- **Port**: 11434
- **GPU**: RTX 3090 24GB VRAM
- **Models**: qwen2.5:14b

### Performance Results
| Metric | CPU (NucBox) | GPU (3090) |
|--------|--------------|------------|
| qwen2.5:14b inference | 2+ min timeout | 0.86 seconds |
| Model load | N/A | 3 seconds |
| Total response | Failed | 4.3 seconds |

### Configuration Required

**On Jesse's PC (run at startup):**
```powershell
$env:OLLAMA_HOST = "0.0.0.0:11434"
$env:OLLAMA_MODELS = "D:\models"
ollama serve
```

**Agent config update:**
```json
"models": {
  "providers": {
    "ollama": {
      "baseUrl": "http://192.168.0.220:11434/v1",
      "api": "openai-completions"
    }
  }
}
```

### Path to 100% Local
This architecture enables full local operation:
1. ✅ Network connectivity established
2. ✅ GPU inference confirmed working
3. 🔄 Testing agent capability with qwen2.5:14b
4. ⏳ Pull more models (llama3.1:8b, mistral:7b)
5. ⏳ Create startup script for Jesse's PC
6. ⏳ Test tool calling with local models
