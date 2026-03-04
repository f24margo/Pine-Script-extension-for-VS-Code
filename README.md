# APEX ICC — Integrated Confluence Checker

<div align="center">

**TradingView indicator system for institutional-grade market analysis**

Built on ICT methodology • Pine Script v6

[![Status](https://img.shields.io/badge/Status-Active_Development-yellow)](https://github.com)
[![Version](https://img.shields.io/badge/Version-3.9.5-blue)](https://github.com)
[![License](https://img.shields.io/badge/License-Research-orange)](https://github.com)

</div>

---

## 📋 Table of Contents

- [Current Status](#-current-status)
- [System Concept](#-system-concept)
- [Architecture](#-architecture)
- [Core Concepts](#-core-concepts)
- [Repository Structure](#-repository-structure)
- [Installation](#-installation)
- [Debug Workflow](#-debug-workflow)
- [Known Issues](#-known-issues)
- [Development Log](#-development-log)
- [Methodology Reference](#-methodology-reference)

---

## 🚀 Current Status

**Stage:** Active development — Block-by-block debugging on 1D timeframe

| Component | Status |
|-----------|--------|
| Macro Triad (DXY, SPX, US10Y) | ✅ Working |
| IPDA Zones (60D / 20D) | ✅ Working |
| Weekly Sweep Detection | ✅ Working |
| Repainting Detector | ✅ Working |
| Dual Debug Panel (Replay mode) | ✅ Working |
| Macro Upgrade (VIX, MOVE, JPY + EMA confluence) | 🔧 Next |
| ADX Engine (Sleep / Trend / Overheat phases) | 📋 Planned |
| RVOL Block (Relative Volume) | 📋 Planned |
| FVG + Liquidity (EQH/EQL) | 📋 Planned |

---

## 💡 System Concept

**APEX ICC (Integrated Confluence Checker)** is a high-tech signal aggregator for TradingView (Pine Script v5/v6). The system solves the problem of **"analysis paralysis"** by combining macroeconomics, market structure, momentum, and timing into a single numerical **Score**.

### System Verdict

* 🟢 **ENTER** — Full confluence of all layers
* 🟡 **WAIT** — Market is uncertain, insufficient conditions
* 🔴 **AVOID** — High risks or lack of structure

---

## 🏗️ Architecture

### Seven Levels of Analysis (Pipeline)

The system is built as a layered pipeline. Each layer adds one dimension of context before a trade decision is made.

#### **Current Implementation**

```
┌─────────────────────────────────────────────────────────────┐
│ Layer 1: MACRO        DXY + SPX + US10Y                     │
│                       Market environment assessment          │
├─────────────────────────────────────────────────────────────┤
│ Layer 2: WEEKLY       Weekly High/Low sweep detection       │
│                       Liquidity grab identification          │
├─────────────────────────────────────────────────────────────┤
│ Layer 3: IPDA         60D/20D equilibrium zones             │
│                       Discount / Premium determination       │
├─────────────────────────────────────────────────────────────┤
│ Layer 4: VERDICT      Confluence of all active layers       │
│                       Final decision logic                   │
└─────────────────────────────────────────────────────────────┘
```

#### **Planned Layers**

```
┌─────────────────────────────────────────────────────────────┐
│ Layer 5: TIMING       ADX phases + Killzones                │
├─────────────────────────────────────────────────────────────┤
│ Layer 6: RVOL         Relative volume confirmation          │
├─────────────────────────────────────────────────────────────┤
│ Layer 7: FVG          Fair Value Gaps + EQH/EQL liquidity   │
└─────────────────────────────────────────────────────────────┘
```

### Foundation Layer (L0)

The system uses **UDT (User Defined Types)** for data encapsulation, eliminating global variable pollution and ensuring modularity.

```pinescript
type ApexScore
    int total     = 0
    int macro     = 0
    int structure = 0
    int timing    = 0
    int sentiment = 0    // Reserved for V3.1
```

---

## 🎯 Core Concepts

### IPDA (Interbank Price Delivery Algorithm)

Price is expected to return to equilibrium — the midpoint of the 60-day range.

- **Below equilibrium** = Discount zone (Long bias)
- **Above equilibrium** = Premium zone (Short bias)

### Weekly Sweep

Price briefly breaks a previous weekly High or Low, grabs liquidity, and returns. This is the trigger condition.

> ⚠️ **Without a sweep — no signal**

### Confluence Rule

A valid setup requires **all three conditions simultaneously:**

1. ✓ Macro environment supports direction
2. ✓ Price is in the correct IPDA zone  
3. ✓ Weekly sweep has occurred

### Repainting (Known Issue in V3.9.5)

Original code uses `lookahead_on` for weekly levels:

- **1D timeframe:** diff ≈ 0.8 ATR — acceptable for research
- **15M timeframe:** diff ≈ 10 ATR — data is unreliable

**Fix implemented:** `high[1]` + `lookahead_off` in debug version

---

## 📁 Repository Structure

```
apex-icc/
├── indicators/
│   └── APEX_V395_REPLAY_DEBUG_FINAL.pine   # Main debug indicator
│
├── strategies/
│   └── (pending — after indicator logic is validated)
│
├── tests/
│   └── APEX_V395_REPAINT_DETECTOR_v2.pine  # Repainting test harness
│
├── docs/
│   ├── research_journal/                    # ETHUSDT_2025.csv data
│   ├── observations.md                      # Replay mode notes
│   └── calibration.md                       # VIX/DXY thresholds
│
└── README.md                                # This file
```

---

## 📦 Installation

### Step-by-Step Guide

1. Open **TradingView Pine Editor**
2. Select all existing code: `Cmd+A` (Mac) / `Ctrl+A` (Windows)
3. Delete
4. Open the `.pine` file in **plain text editor**:
   - Mac: TextEdit
   - Windows: Notepad
   - Linux: nano / vim / gedit
5. Select all, copy
6. Paste into Pine Editor
7. Click **Add to chart**

> ⚠️ **Important:** Always open `.pine` files in a plain text editor. Opening in Word, Google Docs, or AI tools adds invisible characters that break the Pine parser.

---

## 🔬 Debug Workflow

The primary development tool is TradingView **Replay mode** on **1D timeframe**.

### Process

```
1. Load indicator in Pine Editor
2. Enable Replay mode on chart
3. Step through history bar by bar
4. Watch both panels update in real time:
   
   LEFT  panel — working values (what the system decides)
   RIGHT panel — debug values  (why it decided that)

5. Record observations
6. Adjust logic
7. Repeat
```

> **Strategy Tester** is used only after indicator logic is visually confirmed.

### Debug Panels

| Panel | Purpose | Data |
|-------|---------|------|
| **Work Panel** (Left) | Real-time decision | Status, Trend, Zone, Macro signals |
| **Debug Panel** (Right) | Diagnostic data | Raw values, distances, ATR calculations |

---

## ⚠️ Known Issues

| Issue | Location | Status | Impact |
|-------|----------|--------|--------|
| `lookahead_on` repainting | Block 2, weekly levels | Detected, fix in debug version | 1D: ~0.8 ATR, 15M: ~10 ATR |
| Macro data lag on sub-hourly TF | Block 1, `request.security("60")` | Known limitation | 1D timeframe only for now |
| No stop-loss logic | Strategy | Intentional | Logic validation first |

---

## 📝 Development Log

| Date | What Changed |
|------|--------------|
| 2026-02-27 | Repainting detector built and confirmed on ETH 1D |
| 2026-02-27 | Dual panel debug monitor working in Replay mode |
| 2026-02-27 | Weekly sweep fix: `high[1]` + `lookahead_off` |
| 2026-02-28 | Confirmed 15M repainting = 10 ATR — unusable |
| 2026-02-28 | GitHub repo created, VS Code workflow established |
| 2026-03-04 | CSV data structure standardized to 26 columns |

---

## 📚 Methodology Reference

Based on **ICT (Inner Circle Trader)** concepts:

- ✓ IPDA delivery zones
- ✓ Weekly liquidity pools
- ✓ Fair Value Gaps
- ✓ Kill Zones (session-based timing)

### Resources

- [ICT Mentorship](https://www.youtube.com/@InnerCircleTrader)
- [Smart Money Concepts](https://www.tradingview.com/scripts/smartmoneyconcepts/)

---

## ⚖️ Disclaimer

> **This is a research and development project.**
> 
> - Not financial advice
> - Not a production trading system
> - Use at your own risk
> - Always backtest thoroughly
> - Never risk more than you can afford to lose

---

## 📄 License

Research and Educational Use Only

---

## 🤝 Contributing

This is currently a private research project. Contributions are not being accepted at this time.

---

## 📧 Contact

For research collaboration or questions:
- Open an issue in this repository
- Include detailed description and code samples

---

<div align="center">

**Built with 📊 by traders, for traders**

⭐ Star this repo if you find it useful

</div>
