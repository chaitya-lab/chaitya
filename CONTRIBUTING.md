# Contributing to Chaitya App

## Golden Rule

**Everything is an adapter.** If a feature can live in an adapter, it must be an adapter. The kernel (core/) is never touched from this repo.

## Repository Boundaries

| Repo | What lives here | Touch from chaitya-app? |
|------|----------------|------------------------|
| `core/` | Kernel, sessions, events, pipeline, store | **No.** File issues/PRs there. |
| `unigate/` | Messaging exchange, channels, routing | **No.** File issues/PRs there. |
| `chaitya/` | App adapters, agents, skills, config | **Yes — this is where you work.** |

## Project Setup

```bash
# Clone all three repos into sibling directories
# Install core + unigate first
cd core && pip install -e ".[dev]" -e ./sdk && cd ..
cd unigate && pip install -e ".[dev]" && cd ..

# Then this project
cd chaitya
python -m venv .venv
.venv\Scripts\Activate.ps1      # Windows
# source .venv/bin/activate     # macOS/Linux
pip install -e ".[dev,anthropic]"
```

## How to Add a New Adapter

1. Create a directory under `src/chaitya_app/adapters/<name>/`
2. Add `__init__.py` with the adapter registration (using `@adapter` decorator or `module.json`)
3. Follow the Chaitya Core adapter contract (see `core/docs/adapters-dev.md`)
4. Register the entry point in `pyproject.toml` under `[project.entry-points."chaitya.adapters"]`
5. Add tests under `tests/test_<name>.py`

## How to Add a New Agent

1. Create a YAML file in `agents/<name>.yaml`
2. Follow the agent definition schema (see `docs/architecture.md`)
3. The supervisor can immediately invite it — no code changes needed

## How to Add a New Skill

1. Create a YAML file in `skills/<name>.yaml`
2. Follow the skill definition schema
3. Reference it from agent YAML files under `skills:`

## Code Style

- Python 3.11+, type hints everywhere
- `ruff` for linting, `mypy --strict` for types
- 100 char line length
- Tests required for all adapter logic

```bash
ruff check src/ tests/
mypy src/
pytest tests/ -v
```

## Commit Convention

```
<type>: <short description>

Types: init, feat, fix, refactor, docs, test, chore
```

## Architecture Decisions

If you're making a design choice that affects multiple adapters, document it in `docs/` before implementing. Adapter boundaries matter — keep adapters independent.
