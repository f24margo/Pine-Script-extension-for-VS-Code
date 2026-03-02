APEX-ICC Project
Architecture
indicators/ — Основные инструменты анализа.
strategies/ — Торговые системы на базе индикаторов.
tests/ — Скрипты для верификации сигналов.

Отличный скриншот — всё работает как задумано. Stats Panel показывает важное наблюдение сразу:

```
Long / Short  → 28 / 164
In Discount   → 14.8%
In Premium    → 85.4%
```

Система сейчас генерирует шорты в 6 раз чаще лонгов. Это медвежий рынок ETH — логично. Но это также говорит что макро фильтр работает однобоко. Именно это и будет исправлять Этап 2.

---

## Этап 2 — полное описание для Git

### Что входит

**2.1 — Три новых макро датчика**

```
VIX  (CBOE:VIX)   — индекс страха
       < 20        → спокойный рынок
       20-25       → осторожность
       > 25        → предупреждение (WARNING)
       > 35        → паника (PANIC) — блокировка входа

MOVE (CBOE:MOVE)  — волатильность трежерис
       < 100       → норма
       > 100       → повышенный риск облигаций
       > 140       → блокировка входа

JPY  (FX:USDJPY)  — датчик carry trade
       падение > X% за 5 дней → разворот carry → блокировка
       (X — гибкий параметр в настройках)
```

**2.2 — SMA(24) → EMA конфлюенция**

```
Было:   dxy_price > SMA(24)
Станет: dxy_price > EMA(20) AND EMA(20) > EMA(50)
        — оба условия = подтверждённый институциональный тренд
```

Переключается через `i_use_ema` в настройках — можно сравнивать результаты до/после.

**2.3 — Override логика**

```
P1 (жёсткая блокировка):
    VIX > 35 → AVOID независимо от остального
    JPY падение > порога → AVOID

P2 (мягкое ограничение):
    VIX 25-35 → понижаем уверенность, не блокируем
    MOVE > 100 → требуем более сильный IPDA сигнал
```

**2.4 — Обновление Debug Panel**

```
Добавляем в правую панель новый блок:

-- MACRO E2 --
VIX          →  22.4   WARNING
MOVE         →  95.3   OK
JPY 5D chg   →  -1.2%  OK
EMA mode     →  ON/OFF
Override     →  NONE / P1 / P2
```

**2.5 — Обновление Stats Panel в стратегии**

```
Macro E2 blocked → реальные числа (сейчас 0 [OFF])
До/после сравнение Win Rate при включении фильтров
```

---

### Файлы которые меняются

```
indicators/APEX_V395_REPLAY_DEBUG_FINAL.pine  → добавляем блок MACRO E2 в Debug Panel
strategies/APEX_V395_STRATEGY.pine            → активируем f_macro_e2() скелет
tests/                                         → новый тест VIX_FILTER_TEST.pine
```

### Коммиты в Git — план

```
commit 1: "E2: Add VIX, MOVE, JPY data feeds"
commit 2: "E2: Add EMA confluence toggle"
commit 3: "E2: Override P1/P2 logic"
commit 4: "E2: Update Debug Panel with Macro E2 block"
commit 5: "E2: Update Strategy filter + Stats Panel"
commit 6: "E2: Update README"
```

---

Начинаем с индикатора или стратегии?