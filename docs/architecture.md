# Chaitya App — Architecture

## The Three-Layer Stack

```
┌─────────────────────────────────────────┐
│            CHAITYA APP                  │  ← This repo
│  Spaces · Agents · Models · Memory     │
│  Tools · Skills · Heartbeat · Sandbox  │
├─────────────────────────────────────────┤
│         UNIGATE (Communication)        │  ← Separate repo, pip dep
│  Channels · Routing · Delivery         │
├─────────────────────────────────────────┤
│         CHAITYA CORE (Kernel)          │  ← Separate repo, pip dep
│  Dispatch · Sessions · Events · Store  │
└─────────────────────────────────────────┘
```

Chaitya App **never modifies** Core or Unigate. Everything is a standard Core adapter, installed via pip entry points.

## Adapter Architecture

All App-level concepts (spaces, agents, models, memory, tools, skills) are Core adapters. They communicate **exclusively through the kernel event bus**. No adapter calls another adapter directly.

```
Kernel Event Bus
  ├── space.message_posted    → agent adapter picks up
  ├── agent.work_complete     → space adapter updates state
  ├── space.output_ready      → unigate bridge delivers
  ├── model.call_complete     → agent processes response
  └── memory.updated          → space scoped memory stored
```

### Adapter Map

| Adapter | Responsibility |
|---------|---------------|
| **space** | Space lifecycle: create, state machine, participants, message history, sub-spaces |
| **agent** | Agent execution: YAML loading, model call loop, tool dispatch, skill invocation |
| **model** | LLM providers: Anthropic, OpenAI, Ollama. Unified `BaseModelProvider` protocol |
| **memory** | Persistent memory: working, space, episodic, semantic tiers. SQLite default |
| **tools** | Bridge: converts installed Core adapter commands → model function-call schemas |
| **skills** | Workflow engine: named multi-step tool/prompt sequences |
| **unigate_bridge** | Bridge: Unigate message events ↔ Space message history |

### Phase 2 Adapters

| Adapter | Responsibility |
|---------|---------------|
| **sandbox** | Path scoping, network policy, branch contexts |
| **heartbeat** | Cron jobs, watchdog, memory consolidation |
| **mcp** | MCP server exposing tools (Phase 3) |
| **acp** | IDE integration (Phase 3) |

## Data Flow: Message → Response

```
1. Unigate receives message (WhatsApp/Telegram/CLI)
2. unigate_bridge adapter → finds/creates front_desk Space
3. Posts message to Space history → emits space.message_posted
4. supervisor agent activates → reads history
5. supervisor creates sub-spaces → invites specialist agents
6. Agents execute: model call → tool calls → post results
7. Sub-spaces close → summaries bubble up
8. communication_agent drafts reply → supervisor approves
9. space.output_ready → unigate_bridge → Unigate outbox → delivered
```

## Space Model

Spaces are typed, persistent collaborative contexts. See PRD Section 5 for full detail.

**Types:** `front_desk`, `understanding`, `planning`, `execution`, `review`, `output`

**States:** `open` → `active` → `waiting` ↔ `active` → `closed`

## Agent Model

Agents are YAML-defined specialists. Each has: name, model, system prompt, tools, skills, memory scope, permissions.

**Execution loop:** receive invite → load history → load memory → call model → execute tools → post results → done.

## Memory Tiers

| Tier | Scope | Lifetime |
|------|-------|----------|
| `working` | Space | Space lifetime |
| `space` | Space | Until close |
| `episodic` | Global | Permanent |
| `semantic` | Global | Permanent (keyed) |

## Directory Layout

```
src/chaitya_app/
├── adapters/
│   ├── space/           # Space state machine, history, participants
│   ├── agent/           # Agent YAML loader, execution engine
│   ├── model/           # LLM provider abstraction
│   │   └── providers/   # anthropic.py, openai.py, ollama.py
│   ├── memory/          # Memory system
│   │   └── backends/    # sqlite.py
│   ├── tools/           # Adapter→tool schema converter
│   ├── skills/          # Workflow engine
│   └── unigate_bridge/  # Channel ↔ Space bridge
agents/                  # Default agent YAML definitions
skills/                  # Default skill YAML definitions
config/                  # Default configs (space_types.yaml, models.yaml)
tests/                   # Tests
```
