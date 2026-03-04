
# APEX ICC V3.0 — CORE FULL STABLE
> **Техническая документация по архитектуре системы**

**Аудитория:** Инженер-программист (Pine Script / TradingView) · Финансовый аналитик  
**Версия документа:** 1.0 | **Базовый код:** `APEX_ICC_V3_CORE_STABLE.pine`

---

## 1. Концепция системы
**APEX ICC (Integrated Confluence Checker)** — это высокотехнологичный агрегатор сигналов для TradingView (Pine Script v5). Система решает проблему «аналитического паралича», объединяя макроэкономику, рыночную структуру, моментум и тайминг в единый числовой **Score**.

**Вердикт системы:**
* 🟢 **ENTER** — Полная конфлюэнция всех слоев.
* 🟡 **WAIT** — Рынок неопределен, недостаточно условий.
* 🔴 **AVOID** — Высокие риски или отсутствие структуры.

---

## 2. Архитектура: Семь уровней анализа (Pipeline)

### L0: FOUNDATION (Основание)
Использование **UDT (User Defined Types)** для инкапсуляции данных. Это исключает глобальное засорение переменных и обеспечивает модульность.

```pinescript
type ApexScore
    int total     = 0
    int macro     = 0
    int structure = 0
    int timing    = 0
    int sentiment = 0 // Reserved for V3.1






# APEX ICC V3.0 — CORE FULL STABLE
> **Техническая документация по архитектуре системы**

**Аудитория:** Инженер-программист (Pine Script / TradingView) · Финансовый аналитик  
**Версия документа:** 1.0 | **Базовый код:** `APEX_ICC_V3_CORE_STABLE.pine`

---

## 1. Концепция системы
**APEX ICC (Integrated Confluence Checker)** — это высокотехнологичный агрегатор сигналов для TradingView (Pine Script v5). Система решает проблему «аналитического паралича», объединяя макроэкономику, рыночную структуру, моментум и тайминг в единый числовой **Score**.

**Вердикт системы:**
* 🟢 **ENTER** — Полная конфлюэнция всех слоев.
* 🟡 **WAIT** — Рынок неопределен, недостаточно условий.
* 🔴 **AVOID** — Высокие риски или отсутствие структуры.

---

## 2. Архитектура: Семь уровней анализа (Pipeline)

### L0: FOUNDATION (Основание)
Использование **UDT (User Defined Types)** для инкапсуляции данных. Это исключает глобальное засорение переменных и обеспечивает модульность.

```pinescript
type ApexScore
    int total     = 0
    int macro     = 0
    int structure = 0
    int timing    = 0
    int sentiment = 0 // Reserved for V3.1



# APEX ICC — Integrated Confluence Checker

TradingView indicator system for institutional-grade market analysis.
Built on ICT methodology. Pine Script v6.

---

## Current Status

**Stage: Active development — Block-by-block debugging on 1D timeframe**

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

## Repository Structure

```
apex-icc/
├── indicators/
│   └── APEX_V395_REPLAY_DEBUG_FINAL.pine   # Main debug indicator
├── strategies/
│   └── (pending — after indicator logic is validated)
├── tests/
│   └── APEX_V395_REPAINT_DETECTOR_v2.pine  # Repainting test harness
└── README.md
├── docs/
│   ├── research_journal/    # Данные ETHUSDT_2025.csv
│   ├── observations.md      # Заметки по Replay
│   └── calibration.md       # Пороги VIX/DXY
```

---

## Architecture

The system is built as a layered pipeline.
Each layer adds one dimension of context before a trade decision is made.

```
Layer 1: MACRO        DXY + SPX + US10Y — market environment
Layer 2: WEEKLY       Weekly High/Low sweep detection
Layer 3: IPDA         60D/20D equilibrium zones — Discount / Premium
Layer 4: VERDICT      Confluence of all active layers
```

Planned layers:

```
Layer 5: TIMING       ADX phases + Killzones
Layer 6: RVOL         Relative volume confirmation
Layer 7: FVG          Fair Value Gaps + EQH/EQL liquidity levels
```

---

## Core Concepts

**IPDA (Interbank Price Delivery Algorithm)**
Price is expected to return to equilibrium — the midpoint of the 60-day range.
Below equilibrium = Discount zone (Long bias).
Above equilibrium = Premium zone (Short bias).

**Weekly Sweep**
Price briefly breaks a previous weekly High or Low, grabs liquidity, and returns.
This is the trigger condition. Without a sweep — no signal.

**Confluence rule**
A valid setup requires all three conditions simultaneously:
- Macro environment supports direction
- Price is in the correct IPDA zone
- Weekly sweep has occurred

**Repainting (known issue in V3.9.5)**
Original code uses `lookahead_on` for weekly levels.
On 1D timeframe: diff = ~0.8 ATR — acceptable for research.
On 15M timeframe: diff = ~10 ATR — data is unreliable.
Fix implemented in debug version: `high[1]` + `lookahead_off`.

---

## Debug Workflow

The primary development tool is TradingView **Replay mode** on 1D timeframe.

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

Strategy Tester is used only after indicator logic is visually confirmed.

---

## How to Install

1. Open TradingView Pine Editor
2. Select all existing code `Cmd+A` / `Ctrl+A`
3. Delete
4. Open `.pine` file in TextEdit (Mac) or Notepad (Windows)
5. Select all, copy
6. Paste into Pine Editor
7. Click **Add to chart**

> Important: always open `.pine` files in a plain text editor.
> Opening in Word, Google Docs, or AI tools adds invisible characters
> that break the Pine parser.

---

## Known Issues

| Issue | Location | Status |
|-------|----------|--------|
| `lookahead_on` repainting | Block 2, weekly levels | Detected, fix in debug version |
| Macro data lag on sub-hourly TF | Block 1, `request.security("60")` | Known, 1D only for now |
| No stop-loss logic | Strategy | Intentional — logic validation first |

---

## Development Log

| Date | What changed |
|------|-------------|
| 2026-02-27 | Repainting detector built and confirmed on ETH 1D |
| 2026-02-27 | Dual panel debug monitor working in Replay mode |
| 2026-02-27 | Weekly sweep fix: `high[1]` + `lookahead_off` |
| 2026-02-28 | Confirmed 15M repainting = 10 ATR — unusable |
| 2026-02-28 | GitHub repo created, VS Code workflow established |

---

## Methodology Reference

Based on ICT (Inner Circle Trader) concepts:
- IPDA delivery zones
- Weekly liquidity pools
- Fair Value Gaps
- Kill Zones (session-based timing)

This is a research and development project.
Not financial advice. Not a production trading system.
