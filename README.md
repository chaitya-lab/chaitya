# Chaitya App

The intelligence layer of the Chaitya stack. Spaces, agents, memory, tools, and skills — all built as Chaitya Core adapters.

The kernel routes and dispatches. Unigate moves messages. Chaitya App is where reasoning, autonomy, and collaboration live.

## The Stack

```
┌────────────────────────────────────────────────────────┐
│                    CHAITYA APP                         │
│  Spaces · Agents · Models · Memory · Tools · Skills    │
│  Heartbeat · Sandbox · Protocols                       │
├────────────────────────────────────────────────────────┤
│              UNIGATE (Communication)                   │
│  Telegram · WhatsApp · Slack · CLI · WebSocket · Email │
├────────────────────────────────────────────────────────┤
│              CHAITYA CORE (Kernel)                     │
│  Dispatch · Sessions · Events · Pipeline · Store       │
└────────────────────────────────────────────────────────┘
```

## Core Principles

- **The App extends, never modifies.** Everything is a Chaitya Core adapter. No special framework.
- **Spaces are the unit of work.** Every interaction lives inside a Space with typed purpose, state, participants, and history.
- **Agents are specialists.** Each agent has one domain, one model, its own tools and memory scope.
- **The supervisor orchestrates.** Reads context, invites agents, manages Space lifecycle.
- **Chat history IS the coordination.** Agents read and post to Space history. No direct agent-to-agent calls.
- **Memory is first-class.** Queried before model calls. Updated after every interaction.
- **Observable by design.** Every action is a structured event on the kernel bus.

## Status

`0.1.0a1` pre-alpha. Architecture defined, adapters being built.

## Adapter Map

| Adapter | Purpose |
|---------|---------|
| `chaitya-adapter-space` | Space lifecycle, state, participants, history |
| `chaitya-adapter-unigate` | Bridge: Unigate channels ↔ kernel event bus |
| `chaitya-adapter-model` | LLM connectivity, multi-provider, routing |
| `chaitya-adapter-agent` | Specialist agent execution engine |
| `chaitya-adapter-memory` | Semantic memory, Space-scoped and global |
| `chaitya-adapter-tools` | Kernel adapters → model function-call schemas |
| `chaitya-adapter-skills` | Reusable named workflow library |
| `chaitya-adapter-sandbox` | Path scoping, network policy, branch contexts (Phase 2) |
| `chaitya-adapter-heartbeat` | Scheduled jobs, watchdog, memory consolidation (Phase 2) |
| `chaitya-adapter-mcp` | MCP server (Phase 3) |
| `chaitya-adapter-acp` | ACP IDE interface (Phase 3) |

All are independently installable. All follow the Chaitya Core adapter contract. All communicate through the kernel event bus.

## Requirements

- Python 3.11+
- [Chaitya Core](../core/) `>=0.1.0a1` installed
- [Unigate](../unigate/) `>=0.2.0` installed

## Quick Start

### 1. Install dependencies (from sibling repos)

```bash
# From the parent dev directory
cd core && pip install -e ".[dev]" -e ./sdk && cd ..
cd unigate && pip install -e ".[dev]" && cd ..
```

### 2. Install Chaitya App

```bash
cd chaitya
pip install -e ".[dev,anthropic]"
```

### 3. Verify

```bash
chaitya registry list          # should show app adapters
chaitya space list             # empty, no spaces yet
chaitya agent list             # shows default agents
chaitya model list             # shows configured models
```

### 4. First interaction

```bash
chaitya space create front_desk --goal "Hello world test"
chaitya space history <id>
```

## Project Structure

```text
src/chaitya_app/
├── adapters/
│   ├── space/               # Space lifecycle, state machine
│   ├── agent/               # Agent execution engine
│   ├── model/               # LLM provider connectivity
│   │   └── providers/       # anthropic, openai, ollama
│   ├── memory/              # Semantic memory system
│   │   └── backends/        # sqlite, (chroma Phase 2)
│   ├── tools/               # Adapter→tool schema bridge
│   ├── skills/              # Workflow engine
│   └── unigate_bridge/      # Unigate ↔ kernel bridge
agents/                      # Default agent YAML definitions
skills/                      # Default skill YAML definitions
config/                      # Default config templates
tests/                       # Unit and integration tests
docs/                        # Documentation
```

## Documentation

- [Docs Index](docs/README.md)
- [Architecture](docs/architecture.md)
- [Roadmap](ROADMAP.md)
- [Contributing](CONTRIBUTING.md)

## Relationship to Other Repos

| Repo | Role | Touched by App? |
|------|------|-----------------|
| `core/` | Kernel — dispatch, sessions, events, pipeline, store | **Never.** Import via pip. |
| `unigate/` | Messaging — channels, routing, delivery | **Never.** Import via pip. |
| `chaitya/` | Intelligence — spaces, agents, models, memory, tools | **This repo.** |

Each repo is a separate git project. Chaitya App depends on Core and Unigate as pip packages. No source-level coupling.

## License

[MIT](LICENSE)
