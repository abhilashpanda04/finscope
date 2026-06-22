```text
 _____ _       ____
|  ___(_)_ __ / ___|  ___ ___  _ __   ___
| |_  | | '_ \\___ \ / __/ _ \| '_ \ / _ \\
|  _| | | | | |___) | (_| (_) | |_) |  __/
|_|   |_|_| |_|____/ \___\___/| .__/ \___|
                               |_|
```

# finscope

> Terminal-native financial research for stocks, ETFs, and mutual funds — structured data, valuation and risk engines, and optional AI analysis, all without leaving the shell.

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/abhilashpanda04/finscope)
[![Python](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

`finscope` is a command-line research tool **and** a Python library for market analysis. It unifies price history, fundamentals, SEC filings, fund discovery, valuation models, risk analytics, and terminal-first rendering into a single workflow — with an optional AI layer that can summarize filings, build bull/bear cases, and answer natural-language questions about any ticker.

It is built for people who want fast, structured financial research without spreadsheets or browser-heavy tooling.

## Highlights

- **Stock, ETF, and mutual fund** research from the terminal
- **SEC EDGAR** integration for official US filing data (XBRL financials, filings, insider trades)
- **Valuation engine** — Graham, DCF, PEG, relative multiples, Piotroski F-Score, Altman Z-Score
- **Risk engine** — volatility, VaR/CVaR, max drawdown, Sharpe / Sortino / Calmar, beta
- **S&P 500 screener** with a pandas-style query DSL
- **Optional AI analysis** — works with OpenAI, Anthropic, Gemini, Groq, or Mistral (or bring your own model)
- **Interactive dashboard** with arrow-key, segmented menus
- **Python API** with lazy-loaded objects and typed data models
- **HTML export** for shareable reports

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Example Output](#example-output)
- [Core Features](#core-features)
- [CLI Reference](#cli-reference)
- [Interactive Mode](#interactive-mode)
- [AI Analysis](#ai-analysis)
- [Python API](#python-api)
- [Data Sources](#data-sources)
- [Architecture](#architecture)
- [Testing](#testing)
- [Configuration](#configuration)
- [Roadmap](#roadmap)
- [License](#license)

## Installation

### Requirements

- Python 3.11+

### Install from source (recommended)

```bash
git clone https://github.com/abhilashpanda04/finscope.git
cd finscope
uv sync
```

### Install with pip

```bash
pip install -e .
```

### Development install

```bash
uv sync --dev
# or
pip install -e ".[dev]"
```

## Quick Start

### Single-stock overview

```bash
finscope AAPL
```

### Interactive dashboard

```bash
finscope AAPL -i
```

### Valuation and risk

```bash
finscope AAPL valuate
finscope AAPL risk 1y
```

### Compare multiple stocks

```bash
finscope compare AAPL MSFT GOOGL
```

### Screen the S&P 500

```bash
finscope screen "pe < 15 and roe > 20"
```

### Explore funds

```bash
finscope funds
```

### Export an HTML report

```bash
finscope export AAPL
```

### AI analysis (requires an LLM API key)

```bash
export OPENAI_API_KEY=sk-...        # or ANTHROPIC_API_KEY, GEMINI_API_KEY, …
finscope AAPL analyze
finscope AAPL ask "Is it overvalued relative to peers?"
finscope AAPL summarize-filings
```

## Example Output

### Overview

```text
$ finscope AAPL

Loading AAPL...

╭──────────────────────────── Company Overview ────────────────────────────╮
│ Apple Inc. (AAPL)                                                        │
│ Technology / Consumer Electronics                                        │
│ Exchange: NMS  |  Currency: USD                                          │
│                                                                            │
│ USD 230.45  +1.23%                                                       │
╰───────────────────────────────────────────────────────────────────────────╯

  3-Month Trend: ▁▂▃▄▅▆▇█▇▆▅▄▅▆▇

┏━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━┓
┃ Metric               ┃ Value         ┃ Metric               ┃ Value         ┃
┡━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━┩
┃ Market Cap           ┃ $3.42T        ┃ P/E Ratio            ┃ 31.2          ┃
┃ Forward P/E          ┃ 28.7          ┃ ROE                  ┃ 147.3%        ┃
└──────────────────────┴───────────────┴──────────────────────┴───────────────┘
```

### Risk profile

```text
$ finscope AAPL risk 1y

Computing risk profile for AAPL (1y)...

──────────────────────────── Risk Profile: AAPL ────────────────────────────
  ████████████░░░░░░░░ 63/100  Moderate Risk

  Risk Factors
    • Elevated volatility vs market
    • Drawdown exceeds defensive threshold

────────────── Volatility  (src: price history — daily returns) ─────────────
  Annual Volatility:   27.4%  (High)
  30D Volatility:      24.1%
  90D Volatility:      28.8%

──────────── Risk-Adjusted Returns  (src: price history vs risk-free rate) ──
  Annual Return:       +18.6%
  Sharpe Ratio:        0.54
  Sortino Ratio:       0.82
```

### Interactive dashboard

```text
$ finscope AAPL -i

╭──────────────────────── finscope dashboard ─────────────────────────╮
│ Apple Inc.                 USD 230.45            Mkt Cap: $3.42T    │
│ Technology | Hardware      +1.23%               P/E Ratio: 31.2     │
╰──────────────────────────────────────────────────────────────────────╯

Select an option:
  Fundamentals
    ▸ Company Overview
    ▸ Key Ratios
    ▸ Price History & Trend
  Financial Statements
    ▸ Income Statement
    ▸ Balance Sheet
    ▸ Cash Flow Statement
  Advanced Analysis
    ▸ Valuation
    ▸ Risk Profile
    ▸ Dividend Analysis
```

### Funds

```text
$ finscope funds

╭────────────────────────── finscope funds ───────────────────────────╮
│ Mutual Fund & ETF Explorer                                          │
│ Browse curated popular lists or search directly via market sources  │
╰──────────────────────────────────────────────────────────────────────╯

Select a category:
  Indian Funds
    ▸ Indian Mutual Funds (MFAPI.in)
  Global Curated
    ▸ Popular US Funds
    ▸ Global ETFs (LSE)
    ▸ Fixed Income / Bond ETFs
```

## Core Features

### Equity and market research

- Company overview with price snapshot and sparkline
- Key ratios and operating metrics
- Historical OHLCV price data across standard periods
- Recent news and analyst recommendations
- Major holders and ownership breakdown

### Financial statements and filings

- Income statement, balance sheet, and cash flow views
- SEC EDGAR XBRL financial extraction
- Recent SEC filings (10-K, 10-Q, 8-K, …)
- Insider transaction tracking (Forms 3 / 4 / 5)

### Analytics

**Valuation models** — `finscope <ticker> valuate`

- Graham Number (intrinsic value)
- Discounted Cash Flow
- PEG fair value (Peter Lynch)
- Relative valuation (multiples vs. history)
- Piotroski F-Score (9-point financial-strength test)
- Altman Z-Score (bankruptcy-risk indicator)
- Composite verdict with margin of safety

**Risk analytics** — `finscope <ticker> risk`

- Volatility (annualized, 30D, 90D)
- Value at Risk and Conditional VaR
- Max drawdown
- Sharpe, Sortino, and Calmar ratios
- Beta and correlation versus the S&P 500
- Fundamental balance-sheet risk overlay

**Other**

- Dividend analysis and DRIP reinvestment views
- Earnings trend and surprise / beat-rate history
- Peer auto-discovery and side-by-side comparison
- S&P 500 screener (see [CLI Reference](#cli-reference))

### Funds and portfolio workflows

- Global ETF and fund snapshot views
- Fund risk and rolling-return analysis
- Indian mutual fund search and NAV history (MFAPI.in)
- Persistent watchlist
- Portfolio tracking with summary metrics

### AI analysis (optional)

When an LLM provider is configured, finscope adds four AI-powered commands that pull live data through the same provider stack (Yahoo Finance + SEC EDGAR) before reasoning:

- **Comprehensive analysis** — bull/bear case, risk factors, sentiment verdict
- **Ask anything** — natural-language Q&A about a stock
- **Filing summaries** — key highlights and risk factors from recent SEC filings
- **AI comparison** — insight across multiple stocks

See [AI Analysis](#ai-analysis).

### Output

- Interactive terminal dashboard mode
- Rich tables, rules, sparklines, and formatted panels
- Source attribution on every view
- HTML report export
- Programmatic Python API

## CLI Reference

Command pattern:

```text
finscope <ticker> <command> [options]
finscope <keyword-command> [args...]
```

### Ticker subcommands

| Command | Example | Description |
| :--- | :--- | :--- |
| (overview) | `finscope AAPL` | Quick company overview |
| `ratios` | `finscope AAPL ratios` | Key financial ratios |
| `price` | `finscope AAPL price 1y` | Historical price table |
| `financials` | `finscope AAPL financials` | Income statement |
| `balance-sheet` | `finscope AAPL balance-sheet` | Balance sheet |
| `cashflow` | `finscope AAPL cashflow` | Cash flow statement |
| `news` | `finscope AAPL news` | Recent headlines |
| `analysts` | `finscope AAPL analysts` | Analyst recommendations |
| `holders` | `finscope AAPL holders` | Ownership breakdown |
| `sec-financials` | `finscope AAPL sec-financials` | SEC XBRL financials (`--category income\|balance\|cashflow`) |
| `sec-filings` | `finscope AAPL sec-filings` | Recent SEC filings |
| `insiders` | `finscope AAPL insiders` | Insider transaction history |
| `valuate` | `finscope AAPL valuate` | Composite valuation (6 models) |
| `risk` | `finscope AAPL risk 1y` | Risk profile and downside metrics |
| `dividends` | `finscope AAPL dividends` | Dividend profile, growth, DRIP |
| `earnings` | `finscope AAPL earnings` | Earnings surprise / beat rate |
| `peers` | `finscope AAPL peers` | Peer discovery and comparison |
| `analyze` | `finscope AAPL analyze` | AI comprehensive analysis *(key required)* |
| `ask` | `finscope AAPL ask "..."` | Ask any question about a stock *(key required)* |
| `summarize-filings` | `finscope AAPL summarize-filings` | AI SEC filing summary *(key required)* |

### Keyword commands

| Command | Example | Description |
| :--- | :--- | :--- |
| `compare` | `finscope compare AAPL MSFT GOOGL` | Side-by-side stock comparison |
| `compare --analyze` | `finscope compare AAPL MSFT --analyze` | AI comparison insight *(key required)* |
| `watchlist` | `finscope watchlist AAPL TSLA NVDA` | Quick (ephemeral) watchlist table |
| `watch` | `finscope watch add AAPL MSFT` | Persistent watchlist management |
| `portfolio` | `finscope portfolio add AAPL 50 142.50` | Portfolio tracking |
| `screen` | `finscope screen "pe < 15"` | S&P 500 screener |
| `funds` | `finscope funds` | Mutual fund and ETF explorer |
| `export` | `finscope export AAPL` | HTML report export (`--output file.html`) |
| `interactive` | `finscope AAPL -i` | Dashboard mode |

### Global flags

| Flag | Purpose |
| :--- | :--- |
| `-i`, `--interactive` | Launch interactive menu mode |
| `-o`, `--output PATH` | Output file (for `export`) |
| `--period PERIOD` | Time period for `price` / `risk` (default `1mo`) |
| `--category CAT` | SEC financials category: `income` \| `balance` \| `cashflow` |
| `--analyze` | Use AI for `compare` |
| `--version` | Print version |

### Supported price periods

```text
1d  5d  1mo  3mo  6mo  1y  2y  5y  10y  ytd  max
```

### Screener query syntax

`finscope screen "<query>"` filters the current S&P 500 using **pandas `DataFrame.query`** syntax over fetched fundamentals. Combine conditions with `and` / `or`; string fields use quotes.

**Available fields:** `sector`, `industry`, `market_cap`, `pe`, `forward_pe`, `pb`, `ps`, `dividend_yield`, `roe`, `roa`, `debt_to_equity`, `current_price`.

```bash
finscope screen "pe < 20 and roe > 15 and sector == 'Technology'"
finscope screen "dividend_yield > 2.5 and debt_to_equity < 1"
```

## Interactive Mode

Interactive mode provides a grouped terminal dashboard for:

- overview and market data
- statements and filings
- advanced analysis (valuation, risk, dividends, earnings, peers, screener)
- AI analysis submenu (when a provider is configured)
- comparison, watchlist, portfolio, and funds

Launch with a ticker:

```bash
finscope AAPL -i
```

Or start without one (you'll be prompted):

```bash
finscope -i
```

## AI Analysis

The AI layer is **opt-in**. It only activates when an LLM provider is configured, and it always grounds its reasoning in live data fetched through finscope's own Yahoo Finance and SEC EDGAR providers — the model receives structured financials, prices, and filings, not just a ticker symbol.

### Providers

Set **one** provider API key in your environment. finscope auto-detects the first available provider in the order below:

| Env var | Model |
| :--- | :--- |
| `OPENAI_API_KEY` | `openai:gpt-4o` |
| `ANTHROPIC_API_KEY` | `anthropic:claude-sonnet-4-20250514` |
| `GEMINI_API_KEY` | `google-gla:gemini-2.0-flash` |
| `GROQ_API_KEY` | `groq:llama-3.3-70b-versatile` |
| `MISTRAL_API_KEY` | `mistral:mistral-large-latest` |

To override the auto-detected choice (any pydantic-ai model string):

```bash
export FINSCOPE_AI_MODEL="openai:gpt-4o-mini"
```

`FINSCOPE_AI_MODEL` always takes priority over the per-provider keys.

### Commands

```bash
finscope AAPL analyze                      # Bull/bear case + risk factors + sentiment
finscope AAPL ask "How leveraged is Apple?"  # Natural-language Q&A
finscope AAPL summarize-filings            # Key highlights from recent SEC filings
finscope compare AAPL MSFT NVDA --analyze  # AI-driven comparison insight
```

If no provider key is set, AI commands print a helpful message and exit without making network calls.

### Python API (async)

```python
import asyncio
import finscope

aapl = finscope.stock("AAPL")

async def main():
    analysis = await aapl.analyze()              # StockAnalysis
    print(analysis.summary, analysis.sentiment)

    answer = await aapl.ask("What's the debt situation?")
    print(answer)

    filings = await aapl.summarize_filings()     # FilingSummary

    insight = await finscope.ai_compare("AAPL", "MSFT", "GOOGL")

asyncio.run(main())
```

## Python API

`finscope` is also a Python library. All data properties are **lazily fetched and cached** on first access — nothing hits the network until you ask for it.

### Stock research

```python
import finscope

aapl = finscope.stock("AAPL")

# ── Market data & fundamentals ──────────────────────────────────────
info      = aapl.info                       # raw Yahoo Finance dict
ratios    = aapl.ratios                     # KeyRatios (typed)
history   = aapl.price_history("1y")        # OHLCV DataFrame
sparkline = aapl.sparkline                  # 3-month price series
news      = aapl.news
financials   = aapl.financials              # income statement
balance_sheet = aapl.balance_sheet
cashflow  = aapl.cashflow
analysts  = aapl.analyst_recommendations
holders   = aapl.holders                    # (breakdown, institutional)

# ── SEC EDGAR ───────────────────────────────────────────────────────
sec_fin   = aapl.sec_financials             # XBRL financials by category
filings   = aapl.sec_filings(count=10)
insiders  = aapl.insider_transactions

# ── Analytics (pure financial logic — no API key needed) ────────────
valuation = aapl.valuate()                  # StockValuation
valuation.verdict                          # "Fairly Valued"
valuation.graham.intrinsic                 # 112.45
valuation.piotroski.score                  # 7

risk      = aapl.risk(period="1y")          # StockRisk
risk.risk_level                            # "Moderate"
risk.risk_adjusted.sharpe_ratio            # 1.12
risk.market.beta                           # 1.24

dividends = aapl.dividends()               # DividendAnalysis
earnings  = aapl.earnings()                # EarningsAnalysis
peers     = aapl.peers()                   # PeerComparison

# ── Multi-stock & export ────────────────────────────────────────────
results   = finscope.compare("AAPL", "MSFT", "GOOGL")   # list[ComparisonData]
compared  = aapl.compare_with("MSFT", "GOOGL")
path      = aapl.export_html("aapl_report.html")
```

### Funds

```python
vwrl = finscope.fund("VWRL.L")

info     = vwrl.info
returns  = vwrl.returns                    # returns by period
risk     = vwrl.risk("1y")                 # FundRisk
analysis = vwrl.analyze()                  # FundAnalysis (expense, rolling returns)
```

### Screener

```python
from finscope import screen

hits = screen("pe < 20 and roe > 15 and sector == 'Technology'")
for h in hits:
    print(h.symbol, h.metrics)
```

## Data Sources

`finscope` uses different providers for different data classes.

| Source | Role | Used for |
| :--- | :--- | :--- |
| **SEC EDGAR** | Primary official source | US XBRL financials, filings, insider trades |
| **Yahoo Finance** | Secondary market-data source | Prices, ratios, analyst sentiment, news, global ETFs/funds |
| **MFAPI.in** | Primary source for Indian funds | Indian mutual fund metadata and NAV histories |

### Sourcing policy

- Official filing data is preferred where available
- Yahoo Finance is used as a broad-coverage market-data layer
- Terminal views include source attribution
- Missing or weak source coverage falls back gracefully rather than failing hard
- AI commands are grounded in data from these same providers, not in the model's parametric memory

### Note on trust and coverage

Yahoo Finance is convenient and broad, but it should not be treated as the sole trust anchor for every field. For high-trust US fundamentals, `finscope` prefers SEC EDGAR where possible. The current architecture also makes it straightforward to add alternative or premium providers in the future.

## Architecture

```text
src/finscope/
├── cli.py                  CLI entry point and interactive dashboard
├── stock.py                High-level Stock and Fund facades
├── services/               Orchestration layer over providers
├── providers/              Data-source adapters (Yahoo, SEC EDGAR, MFAPI)
├── risk/                   Risk models and computations
├── valuation/              Valuation models and composite verdicts
├── fund_analysis/          Fund analysis engines
├── screener/               S&P 500 query engine
├── dividends/  earnings/  peers/  portfolio/  watchlist/   Domain engines
├── ai/                     LLM agents, tools, prompts, provider config
├── ui/                     Rich renderers, formatters, builders
├── models.py               Shared typed data models
├── exceptions.py           Exception hierarchy
└── config.py               Runtime configuration
```

### Design choices

| Pattern | Usage |
| :--- | :--- |
| Strategy | Swap data providers without changing callers |
| Facade | Expose simple `Stock` / `Fund` interfaces over multiple providers |
| Builder | Standardize Rich table creation |
| Command-style CLI actions | Keep menu actions modular and testable |
| Typed dataclasses | Keep output structures explicit and predictable |
| Tool-augmented LLM agents | Ground AI answers in live provider data |

## Testing

Run the default suite (unit + smoke; network tests are deselected by default):

```bash
pytest
```

Run integration tests (real network calls):

```bash
pytest -m integration -v
```

Run with coverage:

```bash
pytest --cov=finscope --cov-report=term-missing
```

Recommended practice:

- use unit and smoke tests for routine local development
- run integration tests when changing providers or network behavior
- validate CLI rendering after changing menus or output models

## Configuration

### Environment variables

| Variable | Default | Purpose |
| :--- | :--- | :--- |
| `SEC_EDGAR_EMAIL` | `finscope-user@example.com` | Contact email in the SEC EDGAR User-Agent |
| `OPENAI_API_KEY` | — | Enable AI analysis with OpenAI |
| `ANTHROPIC_API_KEY` | — | Enable AI analysis with Anthropic |
| `GEMINI_API_KEY` | — | Enable AI analysis with Google Gemini |
| `GROQ_API_KEY` | — | Enable AI analysis with Groq |
| `MISTRAL_API_KEY` | — | Enable AI analysis with Mistral |
| `FINSCOPE_AI_MODEL` | — | Override the auto-detected model (any pydantic-ai model string) |

Examples:

```bash
export SEC_EDGAR_EMAIL=you@example.com
export OPENAI_API_KEY=sk-...
export FINSCOPE_AI_MODEL="anthropic:claude-sonnet-4-20250514"
```

> Only **one** AI provider key is needed. If several are set, finscope picks the first in the provider order shown in [AI Analysis](#ai-analysis); `FINSCOPE_AI_MODEL` always wins.

## Roadmap

Good next steps that fit the current architecture include:

- additional premium market-data providers
- macro and rates dashboards
- options and volatility workflows
- benchmark-relative fund analytics
- portfolio stress testing
- broader official-source replacement for third-party fields
- more AI workflows (sector screening, portfolio review)

## License

MIT

```text
finscope
Terminal-native financial research.
Built for speed. Structured for analysis. Sourced with traceability.
```
