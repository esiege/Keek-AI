# STORY_09_LOCAL_LLM_INTEGRATION

**Status:** IN_PROGRESS  
**Priority:** HIGH  
**Estimate:** TBD  
**Assigned:** Chip  

## Description
Implement local LLM support with manual model switching to reduce Claude dependency while maintaining quality and enabling seamless toggling between local and cloud models.

## Acceptance Criteria

1. **Ollama Integration**: Set up Ollama on NucBox with Qwen2.5-Coder 32B and Llama 3.1 70B models installed and running

2. **Model Registry**: Extend OpenClaw config to support local model definitions with performance profiles (speed, context window, capability ratings)

3. **Session Toggle**: Enable per-session model switching via `session_status(model="provider/model")` without context loss

4. **Agent Configuration**: Allow agent-specific default models in their SOUL files while preserving fallback behavior

5. **Cost Monitoring**: Extend analytics to track local vs cloud usage and cost savings

6. **Performance Benchmarks**: Document response time and quality comparisons between local and cloud models for different task types

7. **Zero-Downtime Switching**: Verify agents can change models mid-conversation without losing personality or context

8. **Global Local-Only Mode**: Implement system-wide toggle to restrict all sessions to local models only (blocks Claude/cloud providers entirely)

## Technical Notes
- Leverage existing OpenClaw model abstraction
- Maintain backward compatibility with current Claude-only setup
- Consider context window limitations (local ~32k vs Claude 200k)
- Global local-only mode should override all model settings system-wide
- Need graceful degradation when local models unavailable in local-only mode

## Definition of Done
- [ ] Ollama running on NucBox with target models
- [ ] OpenClaw config supports local model definitions
- [ ] Manual model switching works across all agent types
- [ ] Cost analytics track local vs cloud usage
- [ ] Performance comparison documentation complete
- [ ] Zero-downtime model switching verified
- [ ] Global local-only mode toggle implemented and tested
- [ ] Backward compatibility maintained

## Dependencies
- Hardware specs verification for optimal model selection
- OpenClaw model provider abstraction review

---
*Created: 2026-02-07 20:49 PST*  
*Requested by: Jesse*