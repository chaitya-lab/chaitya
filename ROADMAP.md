# Chaitya App — Roadmap

## Phase 1 — Foundation

**Goal:** One working Space with a supervisor and one specialist agent, accessible via CLI.

### Adapters

| Adapter | Scope |
|---------|-------|
| `space` | create, history, invite, close, state machine, sub-spaces |
| `model` | Anthropic + Ollama providers |
| `agent` | Execution engine, YAML agent loading, tool-call loop |
| `memory` | SQLite backend, space + global scopes |
| `tools` | file, shell (confirmation-gated) exposed as model tools |
| `unigate-bridge` | CLI channel only |

### Default Agents

- `supervisor` — orchestrates spaces and delegates
- `intake_agent` — understands and classifies requests
- `file_agent` — file operations specialist
- `communication_agent` — composes human-facing replies

### Done When

- [ ] `chaitya space create front_desk --goal "Help user"` → space created
- [ ] Supervisor invited → creates sub-spaces → invites agents → work completes
- [ ] `chaitya space history <id>` shows full message thread
- [ ] `chaitya agent list` shows loaded agents with capabilities
- [ ] `chaitya model call <name> --prompt "..."` returns LLM response
- [ ] `chaitya memory search <query>` returns relevant entries
- [ ] `chaitya watch --all` shows full event trace of a Space flow

---

## Phase 2 — Channels and Full Agent Library

**Goal:** WhatsApp/Telegram end-to-end, full agent library, rich tools, sandbox.

### Adapters

| Adapter | Scope |
|---------|-------|
| `unigate-bridge` | Telegram, WhatsApp, Slack channels |
| `model` | + Gemini, Groq, additional providers |
| `tools` | + browser, web_search, python, email, calendar, api_call |
| `sandbox` | Path scoping, network policy, branch contexts |
| `heartbeat` | Cron, watchdog, memory consolidation |
| `skills` | Full skill library with templated workflows |

### Default Agents (All 10)

supervisor, intake_agent, browser_agent, search_agent, code_agent,
review_agent, communication_agent, memory_agent, data_agent, file_agent

### Done When

- [ ] WhatsApp message → supervisor → agents execute → reply delivered
- [ ] Branch context: create, try risky action, rollback cleanly
- [ ] Morning briefing heartbeat delivers to Telegram
- [ ] `chaitya space search "order 4521"` finds correct space
- [ ] Skills execute multi-step workflows without per-step model reasoning

---

## Phase 3 — TUI and Protocols

**Goal:** Interactive interface, external interoperability, community ecosystem.

### Deliverables

- [ ] TUI: space browser, message streaming, agent activity visualization
- [ ] `chaitya-adapter-mcp` — expose tools as MCP server
- [ ] `chaitya-adapter-acp` — IDE integration
- [ ] Skill sharing registry
- [ ] Multi-user RBAC (scoped by `canonical_user_id` from Unigate)
- [ ] Documentation site

---

## Current Focus

**Phase 1** — building adapter scaffolds, Space state machine, model provider interface, agent execution loop.
