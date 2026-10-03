# StrategyFoundry TODO

## Phase 0 — Foundation

- [ ] Create Python 3.12+ package and CLI.
- [ ] Add LangGraph orchestration runtime.
- [ ] Add typed domain schemas.
- [ ] Add SQLite development storage and async-capable production storage boundary.
- [ ] Add structured logging, run IDs, job IDs, experiment IDs.
- [ ] Add configuration loader with secret-safe environment handling.
- [ ] Add free-only provider gateway.

## Phase 1 — Corpus ingestion

- [ ] Recursive Pine Script discovery.
- [ ] SHA-256 source hashing.
- [ ] Duplicate and near-duplicate detection.
- [ ] Pine version detection.
- [ ] Script type detection.
- [ ] Persistent source catalog.

## Phase 2 — Pine execution

- [ ] Integrate Pine Script interpreter/runtime.
- [ ] Define interpreter capability matrix.
- [ ] Sandboxed execution.
- [ ] Compile/runtime diagnostics.
- [ ] Execution time and memory limits.
- [ ] Deterministic execution metadata.
- [ ] Capture trades, equity curve, and raw runtime output.

## Phase 3 — Baseline screening

- [ ] Standard backtest configuration.
- [ ] Cheap market/timeframe screening.
- [ ] Minimum trade/activity filters.
- [ ] Cache identical experiments.
- [ ] Parallel bounded workers.

## Phase 4 — Deep research

- [ ] Slippage sensitivity.
- [ ] Commission sensitivity.
- [ ] Parameter perturbation.
- [ ] Walk-forward testing.
- [ ] Out-of-sample testing.
- [ ] Cross-symbol testing.
- [ ] Cross-timeframe testing.
- [ ] Regime testing.
- [ ] Monte Carlo analysis.

## Phase 5 — Research intelligence

- [ ] Bias/repainting detector.
- [ ] Strategy fingerprint.
- [ ] Strategy family classifier.
- [ ] Similarity search.
- [ ] Research memory.
- [ ] Champion/challenger evaluation.

## Phase 6 — Ranking and indexing

- [ ] Deterministic score calculation.
- [ ] Score/version provenance.
- [ ] Multi-dimensional rankings.
- [ ] Search index.
- [ ] Top 100 generator.
- [ ] Strategy detail pages.

## Phase 7 — Reporting

- [ ] Generate `reports/TOP_100_STRATEGIES.md`.
- [ ] Generate per-strategy REPORT.md.
- [ ] Equity/drawdown/rolling-risk charts.
- [ ] Failure and coverage reports.
- [ ] Run summary and throughput report.

## Phase 8 — Operations

- [ ] Resume interrupted runs.
- [ ] Retry queues.
- [ ] Dead-letter queue.
- [ ] Worker health.
- [ ] Adaptive concurrency.
- [ ] Provider quota tracking.
- [ ] Dashboard/API.
