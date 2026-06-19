# Algo Trading System

Minimal, file-based algorithmic trading system powered by Python and Binance API.

---

## Team

| Role | Responsibility |
|---|---|
| Founder | Vision, strategy direction, business decisions |
| Technical Co-founder | Architecture, implementation, technical ownership |

**Principle:** Simple, flexible, and non-vague. One source of truth.

---

## Stack

| Layer | Choice |
|---|---|
| OS | Ubuntu (Digital Ocean) |
| Engine | Python |
| Orchestration | Shell scripts + crontab |
| Storage | CSV / Pickle files |
| Frontend | Angular + PrimeNG (pre-built dist, read-only) |

---

## Architecture

Five independent cron jobs, each with a single concern:

```
Cron 1 → fetcher.sh    → Binance API         → data/raw/
Cron 2 → processor.sh  → pandas-TA           → data/processed/
Cron 3 → strategy.sh   → signal generation   → data/signals/
Cron 4 → executor.sh   → risk gate + orders  → data/orders/
Cron 5 → merger.sh     → unified snapshot    → data/final/   ← Angular reads here
```

Each cron reads from the previous stage. No shared writes. No API between engine and frontend.

---

## Data Flow

```
data/
├── raw/          # OHLCV from Binance
├── processed/    # OHLCV + indicators
├── signals/      # Strategy signal output
├── orders/       # Per-run order files
└── final/        # Merged snapshot (Angular source)
```

---

## Config

Single `config.yaml` controls all behavior:

```yaml
timeframe: 5m
live: false        # false = paper trade, true = live
fund: 1000         # allocated capital (USD)
timezone: UTC
profitlock: 2.5    # % profit lock
stoploss: 1.0      # % max loss per trade
symbols:
  - BTC/USDT
```

Secrets (`api_key`, `api_secret`) are loaded from `.env`. Never hardcoded.

---

## Frontend

- 5 tabs: Dashboard / Orders / Market / System / Droplets
- Read-only. Reads from `data/final/`
- Deployed by copying pre-built `dist/` to server. No Node.js required on server.

---

## Portability

- Clone repo → configure `.env` → run. Works on any Ubuntu machine.
- All context, decisions, and architecture live in this file.
