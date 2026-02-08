# Task 1.1: Memory Audit Results

**Date**: 2026-02-07
**Auditor**: Keek

## Files Injected Into Context (Every Session)

| File | Size (KB) | Purpose |
|------|-----------|---------|
| AGENTS.md | 7.68 | Workspace behavior rules |
| SOUL.md | 5.65 | Personality, objectives |
| TOOLS.md | 6.58 | Local environment notes |
| HEARTBEAT.md | 2.12 | Periodic task instructions |
| MEMORY.md | 1.63 | Long-term memory |
| USER.md | 0.97 | Human context |
| IDENTITY.md | 0.62 | Agent identity |
| **TOTAL** | **25.25 KB** | **Injected every session** |

## Memory Directory (memory/*.md)

| File | Size (KB) | Age |
|------|-----------|-----|
| context-investigation.md | 1.20 | Today |
| 2026-02-07.md | 1.93 | Today |
| 2026-02-07_session_summary.md | 1.49 | Today |
| 2026-02-06_final_session_notes.md | 1.48 | Yesterday |
| 2026-02-06_sprint03_progress.md | 1.13 | Yesterday |
| 2026-02-06_hooks_overview.md | 1.80 | Yesterday |
| **TOTAL** | **9.03 KB** | **NOT auto-injected** |

## ChromaDB Status

**Status**: NOT RUNNING
**Collections**: Unknown
**Impact**: memory_search may not work, vector retrieval unavailable

## What Gets Loaded When

1. **Session start**: All 7 workspace files (~25KB) injected into system prompt
2. **memory_search call**: Queries memory/*.md files on-demand
3. **Explicit read**: Only when I call `read` tool

## Redundancies Identified

1. **AGENTS.md (7.68KB)** - Generic instructions, rarely referenced mid-session
2. **TOOLS.md (6.58KB)** - Environment notes, could be queried on-demand
3. **README.md (8.61KB)** - NOT injected but exists, could confuse
4. **Other SOUL files** - PEARL_SOUL.md, CHIP_SOUL.md etc exist in workspace but aren't mine

## Bloat Analysis

| Category | Size | Could Reduce To | Method |
|----------|------|-----------------|--------|
| Workspace injection | 25.25 KB | ~5 KB | Query on-demand, inject only SOUL.md + USER.md |
| Memory files | 9.03 KB | ~2 KB | Ollama summarization, archive old |
| ChromaDB | 0 KB | N/A | Not running |

## Recommendations

### Immediate (no code changes)
1. Trim TOOLS.md - remove verbose sections, keep essentials
2. Trim AGENTS.md - condense to core rules
3. Archive memory files older than 3 days

### Short-term (hook/config changes)
1. Move TOOLS.md, AGENTS.md to on-demand retrieval
2. Start ChromaDB for semantic search
3. Create Ollama summarization pipeline

### Target State
- Baseline injection: ~5KB (SOUL.md + USER.md + minimal context)
- Everything else: retrieved on-demand via memory_search or explicit query
- Old memories: compressed by Ollama, stored in ChromaDB

## Context Cost Calculation

**Current**: 25.25 KB baseline = ~6,300 tokens before any conversation
**Target**: 5 KB baseline = ~1,250 tokens
**Savings**: 80% reduction in baseline context cost

## Dashboard Metrics (for implementation)

### Memory Health Panel
| Metric | Source | Unit |
|--------|--------|------|
| `memory.baseline_injection_kb` | Sum of workspace *.md files | KB |
| `memory.baseline_injection_tokens` | baseline_kb * 250 | tokens |
| `memory.files_count` | Count of memory/*.md | count |
| `memory.files_total_kb` | Sum of memory/*.md | KB |
| `memory.oldest_file_days` | Age of oldest memory file | days |
| `memory.chromadb_status` | ChromaDB health check | up/down |
| `memory.chromadb_collections` | Number of collections | count |
| `memory.chromadb_documents` | Total documents stored | count |

### Compression Metrics (after Ollama integration)
| Metric | Source | Unit |
|--------|--------|------|
| `compression.sessions_summarized` | Count of summarized sessions | count |
| `compression.ratio_avg` | Avg input/output size | ratio |
| `compression.ollama_calls_24h` | Ollama invocations | count |
| `compression.bytes_saved_24h` | Original - compressed | bytes |

### Context Efficiency Panel
| Metric | Source | Unit |
|--------|--------|------|
| `context.baseline_pct` | baseline_tokens / max_tokens | % |
| `context.available_for_conversation` | max - baseline | tokens |
| `context.injection_trend` | 7-day trend of baseline | +/- KB |
