# StrategyFoundry Agent Instructions

## Mission

Build StrategyFoundry as a fast, restartable, deterministic-first research engine for large Pine Script corpora.

## Non-negotiable rules

1. Do not use an LLM to calculate authoritative backtest metrics.
2. Do not overwrite raw source code or historical experiment results.
3. Every artifact must be content-addressed or versioned.
4. Every asynchronous path must use non-blocking IO and bounded concurrency.
5. CPU-heavy work must run outside the event loop.
6. Never allow an individual failed strategy to stop the pipeline.
7. Retry transient failures with bounded exponential backoff; do not endlessly retry deterministic failures.
8. Never silently transform a Pine Script. Derived scripts must reference the original hash and record every change.
9. Never treat an in-sample result as proof of robustness.
10. Free-only model policy is enforced in the provider gateway, not just by convention.
11. API keys must never appear in source, logs, reports, prompts stored in git, or committed configuration.
12. Preserve reproducibility: record strategy hash, data version/hash, runtime version, engine version, configuration hash, scoring version, and provider/model metadata for AI-assisted steps.
13. Prefer deterministic state-machine steps over open-ended agent loops.
14. Keep LLM context small. Pass identifiers, structured summaries, and targeted excerpts instead of entire corpora.
15. Cache model responses only when the prompt/model/schema/version hashes match exactly.

## Async/performance rules

Use Python 3.12+ and structured concurrency.

Preferred pattern:

- asyncio for orchestration/network IO
- bounded semaphores for provider concurrency
- async DB drivers for networked databases
- queues for backpressure
- process pools or external workers for CPU-heavy parsing/backtesting
- streaming reads for large datasets
- Parquet/Arrow for large analytical artifacts
- batch DB writes
- content hashes to skip duplicate work

Never call blocking filesystem, subprocess, database, or network APIs directly from the event loop.

## Agent roles

Agents should be narrow and typed:

- discovery
- Pine classification
- validation
- repair/transformation advisor
- backtest supervisor
- robustness analysis
- bias/repaint detection
- statistical analysis
- clustering
- ranking explanation
- report generation

The orchestrator remains responsible for state transitions and scheduling. Agents do not invent workflow state.
