# 🔬 LLM-Backtest-Framework

> Design proposal for a backtesting framework for LLM/Agent-based trading strategies, with token cost simulation and decision path visualization.

[![Status](https://img.shields.io/badge/Status-Design%20proposal-lightgrey.svg)](ROADMAP.md)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📌 Status

**No implementation yet — this repo currently contains the design and roadmap.** There is no Python package, no code and nothing to install. Everything below describes the *planned* design; APIs and module names are proposals and may change.

## 🎯 Why This Project?

Traditional backtesting frameworks (Backtrader, Zipline, VectorBT) are designed for rule-based strategies. They don't account for:

- **API Latency**: LLM calls take seconds, which affects execution timing
- **Token Costs**: A strategy might be profitable but lose money on API costs
- **Decision Stochasticity**: LLMs can give different outputs for the same input
- **Agent Memory**: Multi-turn reasoning affects future decisions

The goal of this project is to fill that gap.

## 🧭 Planned Design

### Core backtesting (planned)
- Event-driven backtest engine with portfolio/PnL tracking
- Slippage and commission modeling
- Data handlers for CSV/Parquet and API sources

### LLM-specific features (planned)
- ⏱️ **Latency simulation**: configurable API response-delay distributions
- 💰 **Token cost tracking**: per-model pricing and cost-adjusted PnL
- 🌡️ **Temperature sweep**: test robustness across LLM randomness
- 🧠 **Context window tracking**: token usage and context limits
- 🔀 **Multi-model comparison** via a common adapter interface (plus a deterministic mock adapter for tests)

### Analysis (planned)
- Standard metrics (Sharpe, Sortino, max drawdown, win rate) plus LLM metrics (calls, latency, tokens, API cost)
- Decision path recording with reasoning traces
- Attribution of PnL to specific LLM decisions
- HTML report generation

### Proposed module layout

```
llm_backtest/            # proposed — not created yet
├── core/                # engine, data_handler, portfolio, execution
├── llm/                 # base_adapter, provider adapters, mock_adapter, latency_model, cost_tracker
├── strategies/          # base_strategy + examples (sentiment, news reactor, multi-agent consensus)
└── analysis/            # metrics, decision_viz, attribution, report_generator
```

### API sketch (proposed)

```python
# Proposed API — none of these classes exist yet
from llm_backtest import BacktestEngine, LLMStrategy
from llm_backtest.llm import ClaudeAdapter, CostTracker

llm = ClaudeAdapter(model="claude-sonnet-4-20250514", latency_mode="realistic", cost_tracker=CostTracker())

class SentimentStrategy(LLMStrategy):
    def generate_signal(self, market_data, news_data):
        prompt = self.build_analysis_prompt(market_data, news_data)
        return self.parse_signal(self.llm.analyze(prompt))  # latency/cost tracked automatically

engine = BacktestEngine(data="AAPL", start="2024-01-01", end="2024-12-31", initial_capital=100_000)
results = engine.run(strategy=SentimentStrategy(llm=llm), latency_impact=True, temperature_sweep=[0.0, 0.3, 0.7])
print(results.net_pnl, results.total_api_cost)
```

### Targeted integrations (all planned)

- **Data**: Yahoo Finance, Alpha Vantage, Polygon.io, custom CSV/Parquet
- **LLM providers**: OpenAI, Anthropic (Claude), Google (Gemini), DeepSeek, local models (Ollama)

## 🛠️ Development Roadmap

### Phase 1: Core Framework
- [ ] Basic backtesting engine with event-driven architecture
- [ ] Data handlers for CSV, Parquet, and API sources
- [ ] Portfolio tracking with position management
- [ ] Simple execution model with slippage

### Phase 2: LLM Integration
- [ ] Abstract LLM adapter interface (+ mock adapter)
- [ ] OpenAI, Anthropic, and local model support
- [ ] Latency simulation with configurable distributions
- [ ] Token counting and cost tracking per model

### Phase 3: Analysis Tools
- [ ] Standard performance metrics (Sharpe, Sortino, etc.)
- [ ] Decision path recorder and visualizer
- [ ] Profit attribution to specific LLM insights

### Phase 4: Advanced Features
- [ ] Temperature sweep automation
- [ ] Multi-agent strategy support
- [ ] Context window overflow handling
- [ ] Automated report generation (HTML)

### Phase 5: Release
- [ ] Documentation and example strategies
- [ ] PyPI package release

## 🤝 Contributing

Feedback on the design is welcome — please open an issue. See [CONTRIBUTING.md](CONTRIBUTING.md).

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

## 🔗 Related Projects

- [llm-rl-mt5-trading](https://github.com/Caesarcph/llm-rl-mt5-trading) - Live trading system
- [multi-agent-quant](https://github.com/Caesarcph/multi-agent-quant) - Multi-agent consensus trading
