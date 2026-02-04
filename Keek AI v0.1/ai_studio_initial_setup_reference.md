# AI Studio – Initial Setup & Reference

## 1. Purpose
This document defines the **baseline setup, constraints, and mental model** for the AI Studio running on the GMKtec PC. It exists to:
- Preserve design intent
- Prevent over-engineering
- Serve as a stable reference when the system grows

This system is designed as a **single-machine, multi-role AI development studio** optimized for web application development.

---

## 2. Core Principles

### 2.1 Single Machine, Multiple Roles
- There is **one physical machine**
- Roles (PM, Dev, QA, Ops) are **logical personas**, not separate computers
- Parallelism is simulated through **turn-based orchestration**, not raw concurrency

### 2.2 Files Are the Source of Truth
- Persistent state lives in files, not chat
- If something is not written to disk, it does not exist
- Discord is a *signal and decision layer*, not memory

### 2.3 Encapsulation First
- Dedicated Windows user
- Dedicated email identity
- Clear filesystem boundaries
- All access is revocable

---

## 3. Physical & OS Setup

### 3.1 Hardware
- GMKtec mini PC
- Single internal NVMe SSD

### 3.2 Operating System
- Windows (fresh setup)
- Local or studio-only account
- No personal Microsoft, Google, or browser sync

---

## 4. Account & Identity Strategy

### 4.1 Email
- One studio-only Gmail account
- Used only for:
  - Tool sign-ups
  - Account recovery
- No forwarding to personal inbox

### 4.2 GitHub
- Temporary: personal GitHub account used for Copilot
- Commit identity set to studio name/email
- Planned migration to:
  - GitHub Organization
  - Bot account
  - Copilot Business seat

### 4.3 ChatGPT
- Used as infrastructure and design partner
- Long-term intent: separate studio ChatGPT account

---

## 5. Filesystem Layout

All studio activity is contained under a single root.

```
C:\studio\
  docs\          # Specs, plans, reference docs
  prompts\       # Agent prompts and role definitions
  tasks\         # Task definitions and state files
  repos\         # Git repositories
  artifacts\     # Diffs, logs, test reports, builds
  logs\          # Runtime and orchestration logs
  secrets\       # .env files or encrypted credentials
```

Nothing outside this directory is touched by agents.

---

## 6. Collaboration Surface (Discord)

Discord acts as the **studio floor**, not storage.

### 6.1 Channel Model
- #control – human commands and approvals
- #pm – planning, specs, summaries
- #dev – implementation updates
- #qa – test results and bug reports
- #ops – build and deploy output

### 6.2 What Discord Is Used For
- Status updates
- Approvals and decisions
- Error surfacing

### 6.3 What Discord Is NOT Used For
- Long-term memory
- Specs or code storage
- Test result persistence

---

## 7. Role Definitions (Initial)

### 7.1 Producer / Orchestrator
**Responsibilities**
- Routes tasks between roles
- Tracks task state
- Posts updates to Discord

**Writes**
- tasks/
- logs/

---

### 7.2 Product Manager (PM)
**Responsibilities**
- Convert ideas into specs
- Define acceptance criteria
- Prioritize tasks

**Writes**
- docs/
- tasks/

---

### 7.3 Developer (Dev)
**Responsibilities**
- Write and modify code
- Implement features
- Produce build artifacts

**Writes**
- repos/
- artifacts/

---

### 7.4 QA Engineer
**Responsibilities**
- Run tests
- Validate acceptance criteria
- Report bugs with repro steps

**Writes**
- artifacts/
- tests/ (inside repo)

---

### 7.5 Operations (Ops)
**Responsibilities**
- Build and package apps
- Deploy to environments
- Manage rollbacks

**Writes**
- artifacts/
- deploy/ (if needed)

---

## 8. Task Model

Tasks are explicit handoff objects.

Each task:
- Has a unique ID
- Has a single owner at a time
- Moves through states (planned → dev → qa → ops → done)

Task state is stored in `tasks/` as JSON or Markdown.

---

## 9. Tooling Baseline

### 9.1 Installed Tools
- VS Code
- Git for Windows
- Node.js LTS
- Docker Desktop
- PowerShell 7

### 9.2 VS Code Extensions
- GitHub Copilot
- ESLint
- Prettier
- Dev Containers

---

## 10. Non-Goals (Important)

This system does NOT aim to:
- Simulate human emotions
- Run uncontrolled autonomous loops
- Replace judgment entirely
- Expose inbound network services

Human approval remains in the loop.

---

## 11. Growth Path (Future)

Planned upgrades only when justified:
- Bot GitHub account + Copilot Business
- Dockerized role workers
- Lightweight task DB (SQLite)
- Simple web dashboard for visualization

The file + Discord model remains the foundation.

---

## 12. Design Reminder

> This is a production pipeline, not a chat room.
> Clarity beats cleverness.
> State beats conversation.

End of document.

