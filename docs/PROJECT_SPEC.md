# StrategyFoundry Project Specification

## Mission

Turn a large Pine Script corpus into a reproducible strategy research database and continuously generated leaderboard.

## Primary outputs

1. Complete script catalog.
2. Complete execution/backtest history.
3. Searchable research index.
4. Per-strategy reports.
5. `TOP_100_STRATEGIES.md`.
6. Research memory describing recurring patterns and failure modes.

## Functional requirements

### Corpus

- Recursive ingestion.
- Stable IDs.
- SHA-256 source hashes.
- Source/version lineage.
- Duplicate detection.

### Pine analysis

- Pine version.
- Indicator vs strategy classification.
- Strategy declarations.
- Entries/exits.
- Inputs.
- Imported libraries.
- Security/request usage.
- Potential repaint/lookahead risks.
- Unsupported features.

### Interpreter

- Exact runtime version.
- Capability matrix.
- Timeouts.
- Memory limits.
- Isolation.
- Standardized result schema.

### Experiments

Every experiment includes:

    experiment_id
    strategy_version_id
    data_version
    symbol
    timeframe
    start
    end
    initial_capital
    fees
    slippage
    position_sizing
    leverage
    runtime_version
    configuration_hash

### Baseline

Use a single versioned baseline configuration for fair first-pass comparison.

### Robustness

Deep candidates may receive:

- fee perturbation
- slippage perturbation
- parameter perturbation
- walk-forward analysis
- out-of-sample testing
- cross-symbol testing
- cross-timeframe testing
- market-regime analysis
- Monte Carlo simulation

### Bias controls

Flag:

- lookahead
- repainting
- future leakage
- impossible fills
- suspicious data dependencies
- extreme sensitivity to tiny configuration changes
- excessive optimization
- survivorship-related dependencies where detectable

### Metrics

Returns:

    total_return
    CAGR
    annualized_return

Risk:

    max_drawdown
    drawdown_duration
    volatility
    downside_deviation

Trades:

    trade_count
    win_rate
    average_trade
    median_trade
    profit_factor
    expectancy
    payoff_ratio

Risk-adjusted:

    Sharpe
    Sortino
    Calmar
    Omega

Consistency:

    monthly_hit_rate
    yearly_hit_rate
    rolling_sharpe
    regime_consistency

## Ranking

Create a versioned deterministic score using configurable components.

Do not make total return the sole ranking input.

The index should support separate views such as:

- overall research score
- risk-adjusted
- low drawdown
- robustness
- out-of-sample
- consistency
- cross-market

No single metric should overwrite the underlying evidence.

## Index/search

Structured search examples:

    drawdown < 0.20
    Sharpe > 1.5
    trade_count > 200
    profitable across BTC + ETH
    profitable during bear regime

Semantic search examples:

    "trend following strategies using volatility filters"

    "mean-reversion strategies with stable parameters"

## Reports

### Top 100

Generate:

    reports/TOP_100_STRATEGIES.md

Include:

    rank
    strategy
    score
    CAGR
    Sharpe
    Sortino
    max drawdown
    profit factor
    trades
    OOS result
    robustness result
    bias flags

### Detail report

Each strategy report includes:

- source lineage
- classification
- experiment configuration
- raw metrics
- robustness
- OOS
- regime analysis
- bias findings
- score decomposition
- related strategies
- artifact links

## CLI

    pine-research discover
    pine-research classify
    pine-research validate
    pine-research backtest --pending
    pine-research backtest --strategy STRAT-123
    pine-research robustness --pending
    pine-research rank
    pine-research report
    pine-research top 100
    pine-research search "<query>"
    pine-research status
    pine-research failures
    pine-research retry
    pine-research pipeline run

## Configuration

Use YAML/TOML for behavior and environment variables for secrets.

Example model policy:

    providers:
      google:
        enabled: true
        free_only: true
      nvidia:
        enabled: true
        free_only: true
      openrouter:
        enabled: true
        free_only: true
      local:
        enabled: true
        free_only: true

Do not hard-code secret values.

## Future expansion

The architecture must leave room for:

- strategy recombination
- strategy genome representation
- strategy mutation
- ensemble discovery
- portfolio construction
- automated challenger generation
- knowledge-guided search
- paper trading
- live-market monitoring

Those features should consume the research index rather than bypass it.
