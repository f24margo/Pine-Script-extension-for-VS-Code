# Pine Script Repository Setup Guide

## 📂 Repository Structure

Этот репозиторий организован для хранения индикаторов и стратегий TradingView на Pine Script.

### Folders

```
Pine-Script-VS-Code/
├── docs/                    # Документация и гайды
│   └── setup-guide.md
├── indicators/              # Технические индикаторы
│   └── indicator-name/
│       ├── indicator.pine
│       └── README.md
├── strategies/              # Торговые стратегии
│   └── strategy-name/
│       ├── strategy.pine
│       ├── README.md
│       └── backtest-results/
└── README.md
```

---

## 📊 Как добавить свой индикатор

### Структура папки:
```
/indicators/my-indicator/
├── indicator.pine          # код индикатора
├── README.md               # описание и параметры
└── examples/               # (опционально)
    └── screenshot.png
```

### Шаблон README.md для индикатора:

```
# Indicator Name

## Description
Опишите что делает индикатор...

## Parameters
- **Parameter 1**: Описание (по умолчанию: значение)
- **Parameter 2**: Описание (по умолчанию: значение)

## How to Use
1. Добавьте индикатор на график TradingView
2. Настройте нужные параметры
3. Интерпретируйте сигналы...

## Requirements
- TradingView (Free/Pro)
- Pine Script v5

## Notes
- Лучше работает на таймфреймах выше H1
- Используйте с подтверждением других индикаторов
```

---

## 🎯 Как добавить стратегию

### Структура папки:
```
/strategies/my-strategy/
├── strategy.pine           # код стратегии
├── README.md               # описание логики и параметры
├── backtest-results/       # результаты тестирования
│   └── 2024-results.md
└── examples/               # (опционально)
    └── chart.png
```

### Шаблон README.md для стратегии:

```
# Strategy Name

## Description
Опишите торговую логику...

## Trading Logic
- **Entry**: Условие 1 + Условие 2
- **Exit**: Тейк-профит или Стоп-лосс
- **Risk Management**: Размер позиции, стоп-лосс

## Parameters
- **Parameter 1**: Описание (по умолчанию: значение)
- **Parameter 2**: Описание (по умолчанию: значение)

## Backtest Results
- **Period**: 2023-2024
- **Win Rate**: XX%
- **Profit Factor**: X.XX
- **Max Drawdown**: XX%

## How to Use
1. Добавьте стратегию на график
2. Откройте Strategy Tester (требуется Pro)
3. Настройте параметры тестирования
4. Посмотрите результаты

## Requirements
- TradingView Pro (для Strategy Tester)
- Pine Script v5

## Risks & Disclaimers
- Прошлые результаты не гарантируют будущие
- Всегда используйте стоп-лосс
- Тестируйте на разных инструментах и периодах
- Торгуйте с осторожностью!
```

---

## 🚀 Готово к использованию!

Когда добавляете индикатор или стратегию:
1. Создайте папку в `/indicators/` или `/strategies/`
2. Добавьте туда файл `.pine` с кодом
3. Добавьте файл `README.md` с описанием
4. Загрузите на GitHub