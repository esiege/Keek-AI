# UK01: Persistent Memory - Tasks

## Phase 1: Foundation (3 points)

### Task 1.1: Audit Current Memory Usage
- [ ] Map all memory files (MEMORY.md, memory/*.md, ChromaDB)
- [ ] Measure current context cost of memory injection
- [ ] Document what gets loaded when and why
- [ ] Identify redundancies and bloat

### Task 1.2: Set Up Ollama Summarization Endpoint
- [ ] Create script to call Ollama for summarization
- [ ] Test with sample conversation → summary
- [ ] Benchmark: input size vs output size vs time
- [ ] Document compression ratios achieved

### Task 1.3: Design Memory Schema
- [ ] Define structure for stored memories (facts, decisions, preferences, events)
- [ ] Design retrieval interface (what queries, what returns)
- [ ] Plan tiered storage (hot/warm/cold)
- [ ] Document in MEMORY_SCHEMA.md

## Phase 2: Ollama Integration (3 points)

### Task 2.1: Session Summarizer Hook
- [ ] Create hook that fires on session end
- [ ] Ollama summarizes session into key facts/decisions
- [ ] Store summary in memory/*.md or ChromaDB
- [ ] Test: does session N+1 recall session N learnings?

### Task 2.2: Entity Extraction Pipeline
- [ ] Ollama extracts entities (people, projects, preferences) from conversations
- [ ] Store in structured format (JSON or ChromaDB metadata)
- [ ] Build lookup: "what do I know about X?"
- [ ] Test with real session data

### Task 2.3: Memory Compression Job
- [ ] Cron job: Ollama reviews memory/*.md files nightly
- [ ] Compress older entries (>3 days) into summaries
- [ ] Archive raw logs to cold storage
- [ ] Track compression metrics

## Phase 3: Smart Retrieval (2 points)

### Task 3.1: Query-First Architecture
- [ ] Remove MEMORY.md from automatic context injection
- [ ] Implement on-demand memory retrieval via memory_search
- [ ] Only inject relevant snippets, not whole files
- [ ] Measure context reduction

### Task 3.2: Semantic Memory Search
- [ ] Ensure ChromaDB is populated with memory embeddings
- [ ] Test semantic queries ("what did we decide about X?")
- [ ] Tune relevance thresholds
- [ ] Compare: keyword search vs semantic search quality

### Task 3.3: Memory Decay/Archival
- [ ] Define decay rules (age, access frequency, importance)
- [ ] Implement automatic archival of stale memories
- [ ] Create retrieval path for archived memories (slower but available)
- [ ] Test that important things don't get lost

## Definition of Done

- [ ] Session N learnings are recallable in session N+1 without manual file reads
- [ ] Context cost of memory < 5KB baseline (down from ~25KB)
- [ ] Can answer "what did we decide about X last week?" accurately
- [ ] Compression ratio documented and > 5:1
- [ ] All memory operations use Ollama (free) not Claude (paid)
- [ ] **All docs and code committed to GitHub**
- [ ] **All task results documented in scrum folder**
