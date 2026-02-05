# Persona Loading Guide

**Document**: PERSONA_LOADING.md  
**Story**: S02 - Agent Persona Framework  
**Created**: 2026-02-04  
**Version**: 1.0

---

## Overview

This document describes how persona configuration files are loaded and used by the orchestration system.

---

## File Locations

### Persona Files
```
~/.openclaw/workspace/orchestration/config/agents/
├── pm.json           # Product Manager persona
├── dev.json          # Developer persona
├── qa.json           # QA Engineer persona (future)
└── ops.json          # Operations persona (future)
```

### Channel Configuration
```
~/.openclaw/workspace/orchestration/config/
└── channels.json     # Channel routing configuration
```

### Runtime State
```
~/.openclaw/workspace/orchestration/state/
└── orchestration-state.json    # Current turn state
```

---

## Load Process

### When Personas Are Loaded

1. **On Orchestrator Startup**
   - All `.json` files in `config/agents/` are scanned
   - Valid, enabled personas are loaded into memory
   - Invalid files are logged and skipped

2. **On Manual Reload** (future)
   - Trigger via command or API
   - Hot-reload without restart

### Load Sequence

```
1. Scan directory for *.json files
2. For each file:
   a. Parse JSON
   b. Validate against schema
   c. Check enabled == true
   d. Verify id matches filename
   e. Add to persona registry
3. Log loaded personas
4. Initialize agent sessions (if auto-start enabled)
```

### Pseudocode

```javascript
async function loadPersonas() {
  const agentsDir = '~/.openclaw/workspace/orchestration/config/agents/';
  const files = await fs.readdir(agentsDir);
  const personas = {};
  
  for (const file of files) {
    if (!file.endsWith('.json')) continue;
    
    try {
      const content = await fs.readFile(path.join(agentsDir, file));
      const persona = JSON.parse(content);
      
      // Validate
      const expectedId = file.replace('.json', '');
      if (persona.id !== expectedId) {
        throw new Error(`ID mismatch: file=${file}, id=${persona.id}`);
      }
      
      if (!persona.enabled) {
        console.log(`Skipping disabled persona: ${persona.id}`);
        continue;
      }
      
      // Validate required fields
      if (!persona.id || !persona.name || !persona.role || !persona.systemPrompt) {
        throw new Error(`Missing required fields in ${file}`);
      }
      
      personas[persona.id] = persona;
      console.log(`Loaded persona: ${persona.name} (${persona.id})`);
      
    } catch (error) {
      console.error(`Failed to load ${file}: ${error.message}`);
    }
  }
  
  return personas;
}
```

---

## How Personas Are Used

### 1. Turn Manager Registration

When a persona is loaded, it's registered with the Turn Manager:

```javascript
// Register agent with turn manager
turnManager.registerAgent({
  id: persona.id,
  priority: persona.priority || 5,
  timeoutSeconds: persona.turnBehavior?.timeoutSeconds || 60,
  canInterrupt: persona.turnBehavior?.canInterrupt || false,
  interruptTriggers: persona.turnBehavior?.interruptTriggers || []
});
```

### 2. Session Creation

Each persona can be spawned as an OpenClaw sub-agent session:

```javascript
// Spawn agent session with persona's system prompt
const session = await sessions_spawn({
  task: "Initialize as agent persona",
  label: `agent-${persona.id}`,
  // The system prompt from persona.systemPrompt is applied
});
```

### 3. Message Routing

When a message arrives for an agent, the persona data is used:

```javascript
// Route message to agent
await sessions_send({
  label: `agent-${persona.id}`,
  message: `[Channel: ${channelName}]\n${userMessage}`
});
```

### 4. Capability Enforcement

Capabilities can be used to validate agent actions:

```javascript
function canAgentPerformAction(personaId, action) {
  const persona = personas[personaId];
  
  switch (action) {
    case 'write_code':
      return persona.capabilities?.canWriteCode === true;
    case 'create_story':
      return persona.capabilities?.canCreateStories === true;
    case 'approve':
      return persona.capabilities?.canApproveWork === true;
    case 'deploy':
      return persona.capabilities?.canDeploy === true;
    default:
      return true;
  }
}
```

---

## Validation Rules

### Required Fields

| Field | Rule |
|-------|------|
| `id` | Must match filename, lowercase alphanumeric + hyphens |
| `name` | Non-empty string |
| `role` | Non-empty string |
| `systemPrompt` | Non-empty string |

### Optional Field Defaults

| Field | Default |
|-------|---------|
| `priority` | 5 |
| `enabled` | true |
| `turnBehavior.timeoutSeconds` | 60 |
| `turnBehavior.canInterrupt` | false |
| `channels` | [] |

### Validation Errors

| Error | Cause | Resolution |
|-------|-------|------------|
| `JSON parse error` | Invalid JSON syntax | Fix JSON formatting |
| `ID mismatch` | Filename doesn't match `id` field | Rename file or update `id` |
| `Missing required field` | Required field empty/missing | Add missing field |
| `Invalid priority` | Priority not 1-10 | Use valid range |

---

## Error Handling

### Invalid Persona File

```
[ERROR] Failed to load qa.json: Missing required field 'systemPrompt'
[INFO] Continuing with 2 loaded personas: pm, dev
```

**Behavior**: Invalid files are skipped, system continues with valid personas.

### Missing Config Directory

```
[WARN] Config directory not found: ~/.openclaw/workspace/orchestration/config/agents/
[INFO] Creating default directory structure...
```

**Behavior**: Directory created automatically, no personas loaded until files added.

### Duplicate IDs

```
[ERROR] Duplicate persona ID 'pm' in files: pm.json, pm-backup.json
[INFO] Using first file found: pm.json
```

**Behavior**: First file wins, warning logged.

---

## Adding a New Persona

### Step-by-Step

1. **Create the file**
   ```bash
   touch ~/.openclaw/workspace/orchestration/config/agents/qa.json
   ```

2. **Add required content**
   ```json
   {
     "id": "qa",
     "name": "QA Engineer",
     "role": "QA",
     "systemPrompt": "You are a QA Engineer...\n\nWhen your turn is complete, end with TURN_COMPLETE."
   }
   ```

3. **Add optional fields** (capabilities, personality, etc.)

4. **Validate JSON**
   ```bash
   # Check for syntax errors
   cat qa.json | jq .
   ```

5. **Reload or restart orchestrator**
   - Restart: Full reload
   - Hot reload: (future) `orchestrator reload-personas`

6. **Verify**
   ```
   [INFO] Loaded persona: QA Engineer (qa)
   ```

### Quick Template

```json
{
  "id": "CHANGE_ME",
  "name": "CHANGE_ME",
  "role": "CHANGE_ME",
  "priority": 5,
  "systemPrompt": "You are a ROLE for the Keek AI development studio.\n\nYour responsibilities:\n- RESPONSIBILITY_1\n- RESPONSIBILITY_2\n\nWhen your turn is complete, end with TURN_COMPLETE.",
  "capabilities": {
    "canWriteCode": false,
    "canCreateStories": false,
    "canApproveWork": false
  },
  "enabled": true
}
```

---

## Modifying Existing Personas

### Safe Modification Process

1. **Edit the file** directly (personas are file-based)
2. **Validate JSON** syntax
3. **Reload** the orchestrator

### What Can Be Changed Without Issues

- `name` — display only
- `personality` — affects prompts
- `capabilities` — affects validation
- `turnBehavior` — affects timing
- `discord` — display only

### What Requires Careful Handling

- `id` — must also rename file
- `systemPrompt` — affects all future interactions
- `enabled` — immediately stops/starts agent

---

## Disabling a Persona

### Temporary Disable

Set `enabled: false` in the persona file:

```json
{
  "id": "qa",
  "enabled": false,
  ...
}
```

Persona will be skipped on next load.

### Permanent Removal

1. Remove from any channel configurations
2. Delete the persona file
3. Reload orchestrator

---

## Troubleshooting

### Persona Not Loading

**Check**:
1. File is valid JSON (`cat file.json | jq .`)
2. `id` matches filename
3. `enabled` is true (or not set)
4. All required fields present

### Persona Not Responding

**Check**:
1. Persona assigned to channel in `channels.json`
2. Agent session is running
3. Turn manager has agent in queue
4. No errors in orchestrator logs

### System Prompt Not Applied

**Check**:
1. `systemPrompt` field is non-empty
2. Session was created after persona load
3. Try restarting the agent session

---

## Summary

| Topic | Key Point |
|-------|-----------|
| Location | `~/.openclaw/workspace/orchestration/config/agents/*.json` |
| Load time | On startup (future: hot reload) |
| Validation | ID match, required fields, valid JSON |
| Errors | Logged and skipped, system continues |
| Adding new | Create file, validate, reload |
