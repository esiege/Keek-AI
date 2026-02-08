# Memory Schema Design

**Version**: 1.0
**Date**: 2026-02-07

## Memory Tiers

### 🔥 Hot (In Context)
- **What**: Essential identity, current objectives
- **Size**: < 5 KB
- **Files**: SOUL.md, USER.md (trimmed)
- **Loaded**: Every session start
- **Cost**: Always in context

### 🟡 Warm (Quick Retrieval)  
- **What**: Recent decisions, active projects, preferences
- **Size**: < 50 KB total
- **Storage**: ChromaDB vectors + JSON metadata
- **Loaded**: On-demand via memory_search
- **Cost**: Only when queried

### 🧊 Cold (Archive)
- **What**: Historical logs, old sessions, completed projects
- **Size**: Unlimited
- **Storage**: Compressed files in `memory/archive/`
- **Loaded**: Explicit retrieval only
- **Cost**: Near zero (rarely accessed)

## Memory Types

### 1. Facts
Atomic pieces of knowledge that don't change often.

```json
{
  "type": "fact",
  "id": "fact_001",
  "content": "Jesse's email is esiege@gmail.com",
  "category": "contact",
  "subject": "Jesse",
  "confidence": 1.0,
  "source": "session_2026-02-07",
  "created": "2026-02-07T12:00:00Z",
  "accessed": "2026-02-07T14:00:00Z",
  "accessCount": 3
}
```

### 2. Decisions
Choices made that affect future behavior.

```json
{
  "type": "decision",
  "id": "dec_001",
  "content": "Use Ollama for all summarization tasks",
  "context": "Cost efficiency - Ollama is free, Claude is paid",
  "madeBy": "Jesse + Keek",
  "date": "2026-02-07",
  "status": "active",
  "supersedes": null,
  "tags": ["architecture", "cost", "ollama"]
}
```

### 3. Preferences
How Jesse likes things done.

```json
{
  "type": "preference",
  "id": "pref_001",
  "content": "No em dashes in writing",
  "strength": "strong",
  "source": "explicit",
  "created": "2026-02-04"
}
```

### 4. Events
Significant things that happened.

```json
{
  "type": "event",
  "id": "evt_001",
  "summary": "Created Project Ultra-Keek with 5 stories",
  "date": "2026-02-07",
  "participants": ["Jesse", "Keek"],
  "outcome": "47 story points planned",
  "tags": ["project", "planning", "ultra-keek"]
}
```

### 5. Sessions
Compressed summaries of conversations.

```json
{
  "type": "session",
  "id": "sess_2026-02-07_admin",
  "date": "2026-02-07",
  "channel": "admin-chat",
  "summary": "Discussed AI constraints, created Ultra-Keek project...",
  "keyTopics": ["AI philosophy", "context efficiency", "scrum"],
  "decisions": ["dec_001", "dec_002"],
  "tokensUsed": 45000,
  "compressed": true,
  "originalSize": 12000,
  "compressedSize": 2800
}
```

## Retrieval Interface

### Query Types

```javascript
// Find facts about a subject
memory.query({ type: 'fact', subject: 'Jesse' })

// Find recent decisions
memory.query({ type: 'decision', since: '2026-02-01' })

// Semantic search across all types
memory.search("what did we decide about context optimization")

// Get session summaries
memory.sessions({ channel: 'admin-chat', limit: 5 })
```

### Response Format

```json
{
  "results": [...],
  "totalMatches": 12,
  "returned": 5,
  "queryTime": 45,
  "sources": ["chromadb", "memory/*.md"]
}
```

## Storage Layout

```
workspace/
├── SOUL.md           # 🔥 Hot - always loaded
├── USER.md           # 🔥 Hot - always loaded  
├── memory/
│   ├── index.json    # 🟡 Warm - memory metadata
│   ├── facts.json    # 🟡 Warm - extracted facts
│   ├── decisions.json # 🟡 Warm - active decisions
│   ├── preferences.json # 🟡 Warm - user preferences
│   ├── 2026-02-07.md # 🟡 Warm - today's raw log
│   ├── 2026-02-06.md # 🧊 Cold after 3 days
│   └── archive/      # 🧊 Cold - compressed old data
│       ├── 2026-01.json.gz
│       └── sessions/
```

## Decay Rules

| Age | Action |
|-----|--------|
| 0-3 days | Keep as-is (warm) |
| 3-7 days | Summarize with Ollama, keep summary |
| 7-30 days | Move to archive, compress |
| 30+ days | Keep compressed, low-priority retrieval |

## Dashboard Metrics

| Metric | Description |
|--------|-------------|
| `memory.hot_size_kb` | Size of always-loaded files |
| `memory.warm_count` | Number of warm memories |
| `memory.warm_size_kb` | Total warm storage |
| `memory.cold_count` | Archived items |
| `memory.retrieval_avg_ms` | Average query time |
| `memory.hit_rate` | % queries with relevant results |
| `memory.decay_pending` | Items awaiting compression |
