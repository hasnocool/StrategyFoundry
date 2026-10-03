# StrategyFoundry — Refactored Master Implementation Prompt

Build **StrategyFoundry** as an autonomous, high-throughput Pine Script strategy research engine.

I have a large list/corpus of Pine Scripts and want an orchestration agent with specialized subagents that can autonomously process the corpus using a Pine Script interpreter, perform reproducible backtests, build an indexed result database, and continuously generate a Markdown leaderboard containing the current Top 100 strategies.

## AI providers

The project may use these environment variables:

- `GOOGLE_AI`
- `NVIDIA_NIM`
- `OPENROUTER_AI`

Use **free models/endpoints only**.

Never hard-code API keys.

Never commit secrets.

Build a provider gateway that:

1. discovers or loads the currently allowed free models,
2. rejects paid models,
3. applies per-provider quotas/concurrency limits,
4. retries transient failures,
5. falls back automatically,
6. records provider/model/retry metadata,
7. keeps all business logic independent of a specific vendor.

Start with configurable provider priority rather than hard-coding a permanent winner. A practical starting policy is Google free models → NVIDIA free endpoints → OpenRouter free routing → optional local zero-cost model.

## Harness

Use **LangGraph as the orchestration runtime**.

Do not build a giant autonomous LLM loop.

Use a deterministic graph with specialist agents inserted only at stages where model reasoning provides real value.

The system should be:

- asynchronous
- bounded
- restartable
- durable
- observable
- typed
- deterministic where possible
- provider-independent
- efficient with tokens
- efficient with CPU/RAM/disk/network IO

Use Python 3.12+.

## Main workflow

    discover
      ↓
    deduplicate
      ↓
    classify
      ↓
    validate
      ↓
    baseline backtest
      ↓
    cheap screening
      ↓
    deep robustness fan-out
      ↓
    statistical analysis
      ↓
    bias/repaint analysis
      ↓
    deterministic scoring
      ↓
    indexing
      ↓
    report generation
      ↓
    TOP_100_STRATEGIES.md

## Specialist agents

Create narrow, typed agents:

### Discovery Agent

Find scripts, hash them, assign stable IDs, detect changes.

### Pine Classifier Agent

Classify:

- strategy
- indicator
- library
- utility
- broken
- unsupported

Extract only useful metadata.

### Validator Agent

Inspect compile/runtime compatibility and identify blockers.

### Repair Advisor

Suggest fixes for deterministic tooling to apply.

Never silently modify the original source.

### Backtest Supervisor

Schedule and monitor Pine interpreter jobs.

It should not calculate authoritative metrics itself.

### Robustness Agent

Plan/interpret robustness tests but leave numerical calculations to deterministic engines.

### Bias Agent

Analyze for repainting, lookahead, future leakage, unrealistic execution, and suspicious dependencies.

### Statistics Agent

Interpret already-calculated statistics and produce structured observations.

### Clustering Agent

Assign strategy-family labels and semantic fingerprints.

### Report Agent

Turn indexed facts into Markdown reports.

## Important architectural rule

The LLM does **not** determine whether a strategy made money.

The LLM does **not** calculate Sharpe, drawdown, profit factor, CAGR, or other authoritative metrics.

The LLM does **not** invent missing trades.

The deterministic research engine is the source of truth.

## Pine interpreter

Treat the Pine Script interpreter/runtime as an isolated deterministic service.

Capture:

- compile diagnostics
- runtime diagnostics
- execution duration
- memory usage
- trades
- equity curve
- raw metrics
- runtime version
- input configuration

Use hard timeouts and resource limits.

Failed scripts must not halt the run.

## Staged testing

Do not run the most expensive analysis on every script.

### Tier 0

- source hash
- parse
- classify
- compile

### Tier 1

Cheap baseline backtest on a small representative matrix.

### Tier 2

Multi-symbol/timeframe and realistic execution assumptions.

### Tier 3

Only promising candidates:

- walk-forward
- OOS
- parameter perturbation
- fee/slippage sensitivity
- regime analysis
- Monte Carlo
- broader cross-market validation

## Data model

Create durable records for:

- strategies
- strategy versions
- experiments
- backtests
- metrics
- trades
- equity curves
- robustness runs
- walk-forward runs
- regime results
- bias findings
- rankings
- clusters
- agent executions
- provider calls
- failures
- artifacts

Every experiment must be immutable.

## Reproducibility

Store:

- source hash
- strategy version
- Pine runtime version
- backtest engine version
- data version/hash
- config hash
- ranking/scoring version
- timestamp
- provider/model info for AI steps

A completed result must never depend on hidden conversational context.

## Performance requirements

Optimize for throughput.

Use:

- asyncio for network orchestration
- bounded semaphores
- worker queues
- process workers for CPU-heavy work
- async database IO
- batched writes
- streaming large files
- Parquet/Arrow for large result sets
- caching
- content-addressed artifacts
- adaptive concurrency
- retry/backoff
- dead-letter queues

Never perform blocking work directly in the asyncio event loop.

## LLM efficiency

Do not send entire scripts to multiple agents unnecessarily.

Use a progression:

1. metadata only
2. relevant code excerpts
3. full script only when necessary
4. structured result instead of free-form prose

Cache AI results by:

    model
    prompt_version
    input_hash
    output_schema_version

Prefer one useful model call over several overlapping calls.

Use smaller/fast free models for routine tasks and stronger free models only for difficult cases.

## Top 100

Generate:

    reports/TOP_100_STRATEGIES.md

with:

| Rank | Strategy | Score | CAGR | Sharpe | Sortino | Max DD | PF | Trades | OOS | Robustness |
|------|----------|-------|------|--------|---------|--------|----|--------|-----|------------|

Every row must link to a detailed report.

The leaderboard must be generated from database facts, not from LLM memory.

## Detailed strategy report

Generate:

    reports/strategies/<strategy-id>/REPORT.md

containing:

- strategy metadata
- source lineage
- classification
- configuration
- performance
- risk
- trade statistics
- robustness
- OOS
- regime performance
- bias findings
- score decomposition
- warnings
- related strategies
- artifact links

## CLI

Implement:

    pine-research pipeline run
    pine-research status
    pine-research discover
    pine-research classify
    pine-research validate
    pine-research backtest --pending
    pine-research robustness --pending
    pine-research rank
    pine-research report
    pine-research top 100
    pine-research search "<query>"
    pine-research failures
    pine-research retry

## Autonomous behavior

The orchestrator should continuously determine the next useful action from durable state.

It should:

- resume interrupted jobs
- retry transient failures
- stop retrying deterministic failures
- prioritize cheap work before expensive work
- fan out independent robustness tests in parallel
- avoid duplicate experiments
- refresh the leaderboard after new results
- keep a research memory of recurring failures and useful patterns

## Future direction

Design the core so the indexed research corpus can later power:

- strategy recombination
- strategy mutation
- automated strategy generation
- strategy genomes
- ensemble construction
- portfolio research
- champion/challenger evolution
- paper trading

The immediate deliverable is not an autonomous trading bot.

The immediate deliverable is a **fast, reproducible, searchable Pine Strategy Research Foundry** that can process a large corpus and tell me, with evidence, which strategies survived standardized and robustness testing.
