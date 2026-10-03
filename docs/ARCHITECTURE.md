# StrategyFoundry Architecture

## 1. Architecture decision

Use **LangGraph as the orchestration runtime**, not as the implementation of every task.

LangGraph is a strong fit because StrategyFoundry is a long-running, stateful workflow with deterministic stages, parallel fan-out, persistence, checkpoints, resumability, and selective LLM usage.

The project should be structured as:

    LangGraph
        │
        ├── deterministic pipeline nodes
        ├── bounded parallel worker dispatch
        ├── typed agent calls
        └── persisted run state
                 │
                 ▼
          StrategyFoundry services
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
      Pine     Backtest  Index
     runtime    engine    / DB
        │        │        │
        └────────┴────────┘
                 │
                 ▼
             Reports

## 2. Do not build a "swarm" where every step is an LLM

The desired system is a **deterministic research graph with specialist AI workers**.

Most jobs should be ordinary software:

- enumerate files
- hash files
- parse
- compile
- execute
- calculate metrics
- store results
- rank
- render Markdown

LLMs should be called for:

- ambiguous classification
- repair proposals
- semantic explanation
- strategy fingerprint normalization
- family labeling
- failure interpretation
- report synthesis
- research-memory extraction

This reduces latency, token usage, and failure modes.

## 3. Core graph

    DISCOVER
       ↓
    DEDUPE
       ↓
    CLASSIFY
       ↓
    VALIDATE
       ├── invalid ──→ RECORD_FAILURE
       └── valid
             ↓
        BASELINE_SCREEN
             ↓
       ┌─────┴─────┐
       │           │
     reject      candidate
       │           ↓
       │      DEEP_TEST_FANOUT
       │       ├── slippage
       │       ├── commission
       │       ├── parameter stability
       │       ├── walk-forward
       │       ├── OOS
       │       ├── cross-market
       │       ├── timeframe
       │       └── regimes
       │           ↓
       └──────→ ANALYZE
                    ↓
              BIAS_CHECK
                    ↓
                SCORE
                    ↓
               INDEX
                    ↓
               REPORT
                    ↓
             TOP_100_REFRESH

## 4. State

The LangGraph state should contain IDs and compact structured facts, not large datasets.

Example:

    ResearchRunState
      run_id
      strategy_id
      source_hash
      experiment_ids[]
      current_stage
      status
      failure_code
      metric_summary
      robustness_summary
      bias_summary
      score_version
      artifact_refs[]

Large data stays outside graph state:

- source files
- OHLCV
- trades
- equity curves
- charts
- logs

## 5. Work queue

Do not spawn an unconstrained task per script.

Use a bounded queue:

    discovery → pending
                ↓
             priority
                ↓
          worker semaphore
                ↓
        interpreter/backtest
                ↓
            persistence

Concurrency should be adaptive to measured resource usage.

## 6. LLM provider gateway

All model calls go through one interface:

    ModelGateway.generate(
        task_type,
        input,
        schema,
        constraints
    )

The gateway chooses an allowed free model.

Provider priority is configuration-driven. A sensible starting order is:

    Google free endpoint
        ↓
    NVIDIA free endpoint
        ↓
    OpenRouter free routing
        ↓
    local model (optional zero-cost safety fallback)

The gateway must reject any model whose current configuration is not explicitly marked free.

The model catalog should be refreshed because free models and quotas change over time.

## 7. AI task classes

Use different model profiles rather than one large model for everything.

### Fast classification

Used for:

- Pine type classification
- metadata extraction
- failure categorization

Latency is more important than maximum reasoning depth.

### Code reasoning

Used for:

- interpreting Pine logic
- repair suggestions
- unsupported-feature diagnosis

Prefer models with current coding/tool-use capability.

### Research synthesis

Used only after deterministic metrics exist.

Input should be summarized facts, not the entire database.

### Semantic indexing

Use embeddings/reranking only where they materially improve search.

Do not spend model quota embedding every raw artifact blindly.

## 8. Backtesting

The Pine interpreter is a first-class deterministic service.

Interface:

    submit(strategy_version, data_ref, backtest_config)
    → experiment_id

    get_status(experiment_id)

    get_result(experiment_id)
      → trades_ref
      → equity_ref
      → metrics_ref
      → diagnostics_ref

The backtest service must be isolated from the agent runtime.

## 9. Ranking

Ranking must be deterministic and versioned.

Store:

    ranking_version
    input_metric_snapshot
    component_scores
    penalties
    final_score

The LLM may explain a score, but it cannot calculate the authoritative score.

## 10. Index

Start with SQLite for local development and a replaceable storage abstraction.

Analytical artifacts should use columnar formats such as Parquet/Arrow.

Suggested logical tables:

    strategies
    strategy_versions
    experiments
    backtest_metrics
    trades
    equity_curves
    robustness_results
    walk_forward_results
    regime_results
    bias_findings
    scores
    clusters
    agent_runs
    provider_calls
    failures
    artifacts

## 11. Reporting

Every completed strategy gets:

    reports/strategies/<strategy-id>/
      REPORT.md
      metadata.json
      metrics.json
      robustness.json
      trades.parquet
      equity.parquet
      charts/

Global output:

    reports/TOP_100_STRATEGIES.md

The Top 100 report should be regenerated from indexed facts, not from model memory.

## 12. Performance strategy

Fast path:

    cheap deterministic checks
        → cheap backtest
        → eliminate obvious failures
        → expensive tests only for survivors

Key optimizations:

- deduplicate by source hash
- deduplicate experiments by experiment configuration hash
- cache market-data references
- batch DB writes
- stream large artifacts
- reuse interpreter processes where safe
- cap parallelism
- process CPU-heavy work outside asyncio
- avoid sending raw source to LLMs more than once

## 13. Security

Never store credentials in Git.

Expected local configuration:

    GOOGLE_AI=...
    NVIDIA_NIM=...
    OPENROUTER_AI=...

Use `.env` locally if desired, but commit only an example file.

Redact:

- Authorization headers
- API keys
- provider tokens
- sensitive prompt payloads when logs are persistent

## 14. Recovery

Every stage is restartable.

A process crash must not force the system to rerun successful experiments.

Use durable status transitions:

    PENDING
    RUNNING
    SUCCEEDED
    FAILED
    RETRY
    SKIPPED
    CANCELLED

Failures are tied to the smallest failed unit.

## 15. Success criteria

The architecture is successful when a large Pine Script corpus can be processed unattended and the system can recover from:

- interpreter crashes
- provider rate limits
- malformed scripts
- bad market data
- worker crashes
- machine restarts
- partial report generation

without losing completed research.
