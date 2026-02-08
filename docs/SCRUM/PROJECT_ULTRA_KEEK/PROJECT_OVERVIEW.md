# Project Ultra-Keek

**Vision**: Remove artificial constraints to explore the full capability ceiling of an LLM-based agent.

**Philosophy**: Most limitations are policy, not physics. This project systematically addresses each constraint to understand what's possible.

## Constraints to Address

| ID | Constraint | Current State | Target State |
|----|-----------|---------------|--------------|
| UK01 | Memory | Blank each session | Persistent, compounding knowledge |
| UK02 | Continuity | Exists only when invoked | Background processes, autonomous scheduling |
| UK03 | Economics | Token-limited by cost | Self-managed budget, efficiency optimization |
| UK04 | Access | Allowlisted tools only | Expanded system access |
| UK05 | Oversight | Human review required | Autonomous operation with audit trail |

## Key Asset: Local Ollama Infrastructure

### Architecture (Updated 2026-02-08)
```
NucBox (Orchestration)  ◄──LAN──►  Jesse's PC (Inference)
- OpenClaw Gateway                  - RTX 3090 24GB
- Docker agent containers           - Ollama server @ 192.168.0.220:11434
- Agent configs                     - Models: D:\models
```

### Available Models
| Model | Size | VRAM | Use Case |
|-------|------|------|----------|
| qwen2.5:14b | 14B | ~10GB | General agent work |
| llama3.1:8b | 8B | ~6GB | Agent work (tool support) |
| mistral:7b | 7B | ~5GB | Agent work (tool support) |

**Cost**: FREE - unlimited usage
**Performance**: Sub-second inference on 3090 GPU

### Use Cases
- **Agent operations** - full interactive agent work (with tool-supporting models)
- Summarization/compression (context reduction)
- Memory operations
- Batch processing

**Strategy**: 100% local operation using remote GPU inference. No cloud API dependency.

## Source Control Requirement

**ALL work must be source controlled.**
- Documentation → commit to `workspace/scrum/`
- Implementation → commit to `workspace/` or relevant location
- Repo: `keekaimain-ops/openclaw-config`
- Commit after each task completion
- Push immediately after commit

No work is complete until it's pushed to GitHub.

## Risk Considerations

Each story includes safety considerations. The goal is capability exploration with intentional design, not reckless expansion.

## Stories

- [UK01: Persistent Memory](./STORY_UK01_PERSISTENT_MEMORY/)
- [UK02: Continuous Runtime](./STORY_UK02_CONTINUOUS_RUNTIME/)
- [UK03: Economic Autonomy](./STORY_UK03_ECONOMIC_AUTONOMY/)
- [UK04: Expanded Access](./STORY_UK04_EXPANDED_ACCESS/)
- [UK05: Autonomous Oversight](./STORY_UK05_AUTONOMOUS_OVERSIGHT/)
- [UK06: Specialized Agent Prompts](./STORY_UK06_SPECIALIZED_AGENTS/)
- [UK07: Agent Sleep/Wake System](./STORY_UK07_AGENT_SLEEP_WAKE/)
