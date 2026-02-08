# UK01: Persistent Memory

**Status**: IN PROGRESS  
**Points**: 8  
**Priority**: High (foundational)
**Repo**: `keekaimain-ops/openclaw-config`
**Path**: `workspace/scrum/PROJECT_ULTRA_KEEK/STORY_UK01_PERSISTENT_MEMORY/`

> ⚠️ **Source Control**: All docs and implementation must be committed and pushed. No work is complete until it's in GitHub.

## Problem

I wake up blank every session. Knowledge only persists if I explicitly write it to files and happen to read them next time. No compounding learning. No reliable recall.

## Current State

- MEMORY.md for long-term notes (manual)
- memory/*.md for daily logs (manual)
- memory_search for semantic recall (reactive)
- ChromaDB available but underutilized

## Target State

- Automatic capture of significant learnings
- Semantic memory that compounds across sessions
- Reliable recall without explicit file reads
- Knowledge graph of entities, relationships, decisions

## Approach Options

1. **Enhanced ChromaDB integration**: Auto-embed conversations, decisions, learnings
2. **Structured knowledge base**: Entity extraction, relationship mapping
3. **Session summarization**: Auto-summarize each session into persistent memory
4. **Hybrid**: Combine vector search with structured data

## Acceptance Criteria

- [ ] Learnings from session N are recallable in session N+1 without manual intervention
- [ ] Can answer "what did we decide about X last week?" accurately
- [ ] Memory scales without linear context growth
- [ ] Can forget/archive intentionally (not just accumulate forever)

## Safety Considerations

- Memory should be auditable (Jesse can see what I "know")
- Should not store sensitive data without explicit consent
- Forgetting mechanism prevents unbounded growth

## Context Cost Analysis

**Current cost**: ~25KB workspace files injected every session + memory_search results on demand
**Problem**: More memory = more context = less efficient

## Suggestions for Reducing Context Size

1. **Semantic retrieval over injection**: Don't load MEMORY.md into context. Query ChromaDB on-demand and inject only relevant snippets
2. **Tiered memory**: Hot (in context) / Warm (quick retrieval) / Cold (archived). Only hot items cost context
3. **Aggressive summarization**: Store summaries, not raw logs. "We decided X" not the whole conversation
4. **Entity extraction**: Store facts, not narrative. "Jesse prefers Y" is cheaper than the conversation that revealed it
5. **Decay mechanism**: Old memories get compressed or archived automatically
6. **Query-first architecture**: Never inject memory proactively. Always ask "what do I need?" first

### Ollama Opportunities (FREE)

- **Summarize conversations** before storing in memory (Ollama compresses, Claude never sees raw logs)
- **Extract entities/facts** from sessions for structured storage
- **Generate embeddings** for semantic search (if supported)
- **Pre-filter memory queries** - Ollama retrieves candidates, only relevant bits go to Claude
- **Nightly memory maintenance** - Ollama reviews and compresses old memories at zero cost
