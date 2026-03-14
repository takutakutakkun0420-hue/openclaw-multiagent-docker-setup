# OpenClaw Multi-Agent Docker Architecture (v2026.3.7)

## The Problem: Native VPS Installs (e.g., Hetzner)
When running multi-agent OpenClaw natively on a single filesystem, automated tools like Claude Code often cross-contaminate paths when trying to fix bugs. This inevitably leads to:
- **Sandbox path errors**
- **Skills loading from the wrong agent's workspace**
- **`sessions_spawn` delegation failures** due to environment variable conflicts.

## The Solution: Containerized Isolation
This repository demonstrates the production-grade architectural pattern to solve these exact issues. By using Docker and `docker-compose`:

1. **Absolute Workspace Isolation:** Each agent gets its own strictly mapped volume (`./workspaces/director` vs `./workspaces/subagent-1`). Physical cross-contamination of skills is impossible.
2. **Internal Network Delegation:** Subagents communicate via Docker's internal DNS (`http://openclaw-director:8000`), fixing `sessions_spawn` routing issues natively without exposing unnecessary ports to the public Hetzner IP.
3. **Stateless Configuration:** `agents.list` is mounted read-only (`:ro`), preventing subagents from overwriting the global state.

## Usage
```bash
docker-compose up -d
