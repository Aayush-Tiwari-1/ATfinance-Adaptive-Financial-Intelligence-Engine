# ATfinance-Adaptive-Financial-Intelligence-Engine

> **Multi-source news → FinBERT NLP → Adaptive Quant → Forecast Tournament → Master Fusion**  
> A one-click, end-to-end stock intelligence pipeline for the Nifty 50 universe, with a live Streamlit UI.

---

## Overview

ATfinance is an agentic financial analysis system that ingests real-time news, scores it with transformer-based sentiment models, fuses the signal with quantitative technical indicators, runs a multi-model forecasting tournament, and surfaces actionable buy/sell signals — all through a 7-page interactive dashboard.

Built entirely in Python, runnable in Google Colab with a single sequential execution of blocks.

---

## Architecture

```
Multi-Source News Ingestor
  ├── Google News RSS (per-company, 3 search strategies)
  ├── Moneycontrol RSS (pre-fetched, shared)
  └── Economic Times RSS (pre-fetched, shared)
          │
          ▼
Relevance Scoring & Deduplication
  (source credibility + recency decay + keyword weighting + ticker matching)
          │
          ▼
FinBERT Semantic Sentiment Engine   ←── sentence-transformers / HuggingFace
  (bullish / bearish / neutral per article, confidence-weighted aggregation)
          │
          ▼
Adaptive Quant Engine               ←── yfinance + pandas-ta
  (RSI, MACD, Bollinger Bands, ADX, ATR, RVOL, relative outperformance)
          │
          ▼
Time-Series Forecasting Tournament  ←── statsmodels + scikit-learn
  (MA, ARIMA, SES, DES, TES — winner selected per ticker by RMSE on held-out window)
          │
          ▼
Master Alpha Fusion                 ←── XGBoost / weighted ensemble
  (quant score + sentiment score + forecast expected move → Master Signal)
          │
          ▼
Streamlit UI (7 Pages)
  Dashboard | Signals | Forecast | Sentiment | Explainability | Simulator | Learning Hub
```

---

## Features

- **50-stock universe** — full Nifty 50 coverage via NSE tickers
- **~1,000 articles ingested per run** across trusted sources (Reuters, Bloomberg, Moneycontrol, ET, CNBC-TV18, Business Standard)
- **Source credibility weighting** — tiered bonus system with banned-source filtering
- **Recency decay** — 48-hour freshness boost using exponential decay
- **FinBERT NLP** — finance-domain transformer for sentiment scoring (not generic VADER/TextBlob)
- **5-model forecast tournament** — auto-selects the lowest-RMSE model per ticker
- **Explainability-first UI** — every signal shows *why* (which engines agree, what drives confidence)
- **Monte Carlo Simulator** — probability of profit/loss with ATR-calibrated volatility
- **Zero re-downloads** — shared price cache; yfinance called once per session

---

## Pages (Streamlit UI)

| Page | What it shows |
|---|---|
| Dashboard | Master signal leaderboard with Alpha scores |
| Signals | Quant signals — RSI, ADX, RVOL, stop-loss, status |
| Forecast | 5-day price forecasts with winning model per ticker |
| Sentiment | FinBERT article-level sentiment per company |
| Explainability | Why bullish/bearish — engine agreement breakdown |
| Simulator | Monte Carlo profit/loss distribution for a given ticker |
| Learning Hub | Indicator definitions and signal logic glossary |

---

## Tech Stack

| Layer | Libraries |
|---|---|
| Data Ingestion | `feedparser`, `requests`, `beautifulsoup4`, `trafilatura` |
| Market Data | `yfinance`, `pandas-ta` |
| NLP / Sentiment | `transformers`, `sentence-transformers`, `torch` |
| Forecasting | `statsmodels` (ARIMA, Holt-Winters), `scikit-learn` |
| ML Fusion | `xgboost`, `scipy`, `numpy` |
| Visualization | `plotly`, `streamlit` |
| Runtime | Google Colab (Python 3.12) |

---

## Quick Start

```bash
# 1. Open in Google Colab
# 2. Run Block 0 — installs and pins all dependencies
# 3. Run Blocks 1-4 — news ingestion for all 50 companies
# 4. Run Block 5 — FinBERT sentiment scoring
# 5. Run Blocks 6, 7, 7.1, 8, 8.5 — quant, forecast, fusion
# 6. Run UI block — launches Streamlit on port 8501
# 7. Open the printed Colab proxy URL
```

All blocks are idempotent. Price data is cached in memory — re-running downstream blocks does **not** trigger new API calls.

---

## Output Files

All outputs written to `/tmp/agentic_sector/`:

| File | Contents |
|---|---|
| `clean_articles.csv` | Filtered, deduplicated news corpus with relevance scores |
| `article_filter_audit.json` | Ingestion stats — raw vs filtered, top sources |
| `institutional_quant_signals.csv` | Per-ticker quant signals and status |
| `predictive_forecasts.csv` | 5-day forecasts with winning model and expected move |
| `master_fusion.csv` | Final Master Score and signal per ticker |

---

## Signal Logic

**Master Signal** thresholds (adaptive, percentile-based per run):

| Signal | Meaning |
|---|---|
| 🚀 HIGH PRIORITY BUY | Master Score ≥ 2.36 — quant + sentiment + forecast all aligned bullish |
| 📈 POTENTIAL BUY | Moderate cross-engine agreement |
| 👀 WATCHLIST (BULL) | Early bullish setup, wait for confirmation |
| ⚖️ NEUTRAL | Mixed or flat signals |
| ⚠️ WATCHLIST (BEAR) | Early bearish setup |
| 📉 POTENTIAL LOSS | Moderate bearish alignment |
| 🩸 STRONG SELL | Master Score ≤ −3.09 — all engines aligned bearish |

> Thresholds are computed per-run from the score distribution (80th/50th percentiles), making the system self-calibrating across market regimes.

---

## Disclaimer

This tool is for **educational and research purposes only**. It does not constitute financial advice. Always do your own research before making investment decisions.

---

*Created by Aayush Tiwari

