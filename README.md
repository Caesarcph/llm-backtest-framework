# 🔬 LLM-Backtest-Framework

> A specialized backtesting framework for LLM/Agent-based trading strategies with token cost simulation and decision path visualization.

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 🎯 Why This Project?

Traditional backtesting frameworks (Backtrader, Zipline, VectorBT) are designed for rule-based strategies. They don't account for:

- **API Latency**: LLM calls take 1-10 seconds, significantly impacting execution
- **Token Costs**: A strategy might be profitable but lose money on API costs
- **Decision Stochasticity**: LLMs can give different outputs for the same input
- **Agent Memory**: Multi-turn reasoning affects future decisions

**This framework fills that gap.**

## ✨ Features

### Core Backtesting
- 📊 Event-driven and vectorized backtesting modes
- 🔄 Realistic slippage and commission modeling
- 📈 Multi-asset and multi-timeframe support
- 💾 State persistence for long-running backtests

### LLM-Specific Features
- ⏱️ **Latency Simulation**: Configurable API response delays
- 💰 **Token Cost Tracking**: Per-model pricing with detailed cost breakdown
- 🌡️ **Temperature Sweep**: Test strategy robustness across LLM randomness
- 🧠 **Context Window Simulation**: Track token usage and context limits
- 🔀 **Multi-Model Comparison**: Compare GPT-4, Claude, Gemini side-by-side

### Analysis & Visualization
- 📉 Decision path visualization with reasoning traces
- 🎯 Attribution analysis: which LLM insights drove profits?
- 📊 Interactive Plotly dashboards
- 📑 Automated report generation

## 🏗️ Architecture

```
llm-backtest-framework/
├── core/
│   ├── engine.py           # Main backtest engine
│   ├── data_handler.py     # Market data management
│   ├── portfolio.py        # Position & PnL tracking
│   └── execution.py        # Order execution simulation
├── llm/
│   ├── base_adapter.py     # Abstract LLM interface
│   ├── openai_adapter.py   # GPT-4/GPT-4o support
│   ├── anthropic_adapter.py # Claude support
│   ├── mock_adapter.py     # Deterministic testing
│   ├── latency_model.py    # API latency simulation
│   └── cost_tracker.py     # Token usage & pricing
├── strategies/
│   ├── base_strategy.py    # Strategy interface
│   ├── examples/
│   │   ├── sentiment_trader.py
│   │   ├── news_reactor.py
│   │   └── multi_agent_consensus.py
├── analysis/
│   ├── metrics.py          # Sharpe, Sortino, Max DD, etc.
│   ├── decision_viz.py     # Decision path visualization
│   ├── attribution.py      # Profit attribution analysis
│   └── report_generator.py # PDF/HTML reports
├── data/
│   ├── loaders/            # Data source connectors
│   └── sample/             # Sample datasets
├── tests/
├── examples/
└── docs/
```

## 🚀 Quick Start

### Installation

```bash
pip install llm-backtest-framework

# Or from source
git clone https://github.com/Caesarcph/llm-backtest-framework.git
cd llm-backtest-framework
pip install -e .
```

### Basic Usage

```python
from llm_backtest import BacktestEngine, LLMStrategy
from llm_backtest.llm import ClaudeAdapter, CostTracker

# Initialize LLM with cost tracking
llm = ClaudeAdapter(
    model="claude-sonnet-4-20250514",
    latency_mode="realistic",  # Simulates actual API delays
    cost_tracker=CostTracker()
)

# Define your strategy
class SentimentStrategy(LLMStrategy):
    def generate_signal(self, market_data, news_data):
        prompt = self.build_analysis_prompt(market_data, news_data)
        
        # LLM call with automatic latency/cost tracking
        response = self.llm.analyze(prompt)
        
        return self.parse_signal(response)

# Run backtest
engine = BacktestEngine(
    data="AAPL",
    start="2024-01-01",
    end="2024-12-31",
    initial_capital=100000
)

results = engine.run(
    strategy=SentimentStrategy(llm=llm),
    latency_impact=True,  # Account for execution delays
    temperature_sweep=[0.0, 0.3, 0.7]  # Test robustness
)

# Generate report
results.generate_report("backtest_report.html")
print(f"Net PnL: ${results.net_pnl:,.2f}")
print(f"API Costs: ${results.total_api_cost:,.2f}")
print(f"Cost-Adjusted PnL: ${results.net_pnl - results.total_api_cost:,.2f}")
```

### Decision Path Visualization

```python
from llm_backtest.analysis import DecisionVisualizer

viz = DecisionVisualizer(results)

# Show reasoning chain for specific trades
viz.plot_decision_path(
    trade_id=42,
    show_alternatives=True,
    highlight_key_factors=True
)

# Aggregate analysis
viz.plot_decision_heatmap()  # When does LLM perform best?
viz.plot_confidence_vs_accuracy()  # Calibration analysis
```

## 📊 Sample Output

```
================================================================================
                    LLM BACKTEST REPORT - AAPL Sentiment Strategy
================================================================================

Period: 2024-01-01 to 2024-12-31 (252 trading days)
Initial Capital: $100,000.00

PERFORMANCE METRICS
───────────────────
Total Return:        +23.45%
Sharpe Ratio:        1.87
Sortino Ratio:       2.34
Max Drawdown:        -8.23%
Win Rate:            62.3%

LLM-SPECIFIC METRICS
───────────────────
Total LLM Calls:     1,247
Avg Latency:         2.3s (σ=0.8s)
Latency Impact:      -$1,234 (missed fills due to delay)
Total Tokens:        2,847,392
API Cost:            $142.37

COST-ADJUSTED PERFORMANCE
───────────────────
Gross PnL:           $23,450.00
- API Costs:         -$142.37
- Latency Impact:    -$1,234.00
= Net PnL:           $22,073.63

TEMPERATURE SENSITIVITY
───────────────────
Temp 0.0:  Sharpe 1.92, Consistency 94%
Temp 0.3:  Sharpe 1.87, Consistency 89%  ← Selected
Temp 0.7:  Sharpe 1.45, Consistency 71%
```

## 🛠️ Development Roadmap

> 📌 As of 2026-11, [ROADMAP.md](ROADMAP.md) supersedes the phases below. / 2026 年 11 月起以 [ROADMAP.md](ROADMAP.md) 为准。

### Phase 1: Core Framework (Weeks 1-3)
- [ ] Basic backtesting engine with event-driven architecture
- [ ] Data handlers for CSV, Parquet, and API sources
- [ ] Portfolio tracking with position management
- [ ] Simple execution model with slippage

### Phase 2: LLM Integration (Weeks 4-6)
- [ ] Abstract LLM adapter interface
- [ ] OpenAI, Anthropic, and local model support
- [ ] Latency simulation with configurable distributions
- [ ] Token counting and cost tracking per model

### Phase 3: Analysis Tools (Weeks 7-9)
- [ ] Standard performance metrics (Sharpe, Sortino, etc.)
- [ ] Decision path recorder and visualizer
- [ ] Profit attribution to specific LLM insights
- [ ] Interactive Plotly dashboards

### Phase 4: Advanced Features (Weeks 10-12)
- [ ] Temperature sweep automation
- [ ] Multi-agent strategy support
- [ ] Context window overflow handling
- [ ] Automated report generation (PDF/HTML)

### Phase 5: Polish & Release (Weeks 13-14)
- [ ] Comprehensive documentation
- [ ] Example strategies library
- [ ] PyPI package release
- [ ] Tutorial videos

## 🔌 Integrations

| Data Source | Status |
|-------------|--------|
| Yahoo Finance | ✅ Built-in |
| Alpha Vantage | ✅ Built-in |
| Polygon.io | 🔜 Planned |
| Custom CSV/Parquet | ✅ Built-in |

| LLM Provider | Status |
|--------------|--------|
| OpenAI (GPT-4, GPT-4o) | ✅ Built-in |
| Anthropic (Claude) | ✅ Built-in |
| Google (Gemini) | ✅ Built-in |
| Local (Ollama) | 🔜 Planned |
| DeepSeek | 🔜 Planned |

## 🤝 Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Priority Areas
1. Additional LLM provider adapters
2. More realistic latency models based on empirical data
3. Strategy examples from the community
4. Performance optimizations for large backtests

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

## 🔗 Related Projects

- [llm-rl-mt5-trading](https://github.com/Caesarcph/llm-rl-mt5-trading) - Live trading system
- [multi-agent-quant](https://github.com/Caesarcph/multi-agent-quant) - Multi-agent consensus trading

---

**Star ⭐ this repo if you find it useful!**