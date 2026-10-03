# StrategyFoundry

Autonomous Pine Script strategy research and backtesting foundry.

StrategyFoundry ingests a large Pine Script corpus, validates and classifies each script, executes compatible strategies through a Pine Script interpreter, performs staged backtests and robustness analysis, indexes the results, and continuously generates research reports such as `TOP_100_STRATEGIES.md`.

## Core principle

**LLMs orchestrate research; deterministic software produces research facts.**

Backtest metrics, trade records, equity curves, ranking calculations, hashes, deduplication, scheduling, and reproducibility must be deterministic and testable. LLMs are used only for tasks where language-model reasoning is useful: classification assistance, repair suggestions, interpretation, anomaly review, clustering labels, and report synthesis.

## Recommended runtime

The primary orchestration runtime is **LangGraph**. It is a low-level orchestration runtime for long-running, stateful workflows and supports durable persistence, deterministic + agentic steps, streaming, and recovery. That matches StrategyFoundry's staged, restartable research pipeline. [LangGraph documentation](https://docs.langchain.com/oss/python/langgraph/overview)

For typed model-facing workers, Pydantic AI may be used behind the orchestration boundary where useful. Keep provider access behind a StrategyFoundry model gateway so the project remains independent of any one model vendor.

OpenCode/Pi can be useful as **development/coding agents**, but they should not be the production research scheduler. The production system needs durable graph state, bounded parallelism, deterministic jobs, resumability, and database-backed work queues.

## Provider policy

Only explicitly free model/endpoints may be selected.

Configured provider environment variables:

- `GOOGLE_AI`
- `NVIDIA_NIM`
- `OPENROUTER_AI`

Secrets belong in the environment or an ignored local secrets file. Never commit API keys.

The router must verify that a selected model is currently free before use. OpenRouter exposes a free-model collection and a `free` auto-router path; NVIDIA currently exposes free endpoints in its model catalog; Google documents free-tier Gemini API access. Availability and quotas can change, so model IDs must be configuration-driven rather than hard-coded into business logic.

## First target

Run:

    pine-research pipeline run

and produce:

    reports/TOP_100_STRATEGIES.md

along with a complete index of every attempted script, experiment, metric set, failure, and artifact.

## Initial repository layout

    StrategyFoundry/
    ├── AGENTS.md
    ├── README.md
    ├── TODO.md
    ├── pyproject.toml
    ├── configs/
    │   ├── default.yaml
    │   └── models.example.yaml
    ├── docs/
    │   ├── MASTER_PROMPT.md
    │   ├── ARCHITECTURE.md
    │   └── PROJECT_SPEC.md
    ├── src/
    │   └── strategy_foundry/
    │       ├── cli/
    │       ├── orchestration/
    │       ├── agents/
    │       ├── models/
    │       ├── pine/
    │       ├── backtest/
    │       ├── market_data/
    │       ├── experiments/
    │       ├── ranking/
    │       ├── indexing/
    │       ├── reporting/
    │       ├── storage/
    │       ├── providers/
    │       └── telemetry/
    ├── schemas/
    ├── tests/
    └── data/                 # gitignored generated data

See `docs/MASTER_PROMPT.md` for the implementation prompt and `docs/ARCHITECTURE.md` for the runtime design.
