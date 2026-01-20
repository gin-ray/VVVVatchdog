# Техническое Задание: VVVVatchdog (Anti-Lag Edition)

## 1. Обзор проекта
**VVVVatchdog** — система мониторинга (Watchdog) для мультимедийных инсталляций. 
**Цель**: Обеспечить бесперебойную работу приложений, автоматический перезапуск при зависаниях, сбор метрик и нотификации.
**Текущий этап**: Рефакторинг прототипа в модульную архитектуру MVVM.

## 2. Функциональные требования

### 2.1. Мониторинг процессов
- Отслеживание состояния списка процессов (по имени/ID).
- Определение зависших процессов (Not Responding).
- Проверка потребления ресурсов (CPU/RAM) - *Опционально на первом этапе*.

### 2.2. Управление (Watchdog Logic)
- Автоматический перезапуск процесса при падении или зависании.
- Настраиваемые таймауты (время ожидания перед перезапуском).
- "Graceful" завершение процессов перед убийством.
- Ограничение количества перезапусков за период времени (защита от boot loop).

### 2.3. Пользовательский интерфейс (UI)
- Реализация на **VL.ImGUI**.
- Dashboard со списком отслеживаемых приложений и их статусами (Running, Frozen, Stopped, Restarting).
- Лог событий (консоль).
- Системный трей (свернуть/развернуть).
- Редактор настроек.

### 2.4. Конфигурация
- Хранение настроек в XML (сериализация).
- Настройки для каждого приложения: Путь, Аргументы запуска, Таймауты.

### 2.5. Коммуникация
- OSC API для внешнего управления и статусов.

## 3. Архитектура и Структура данных (Model)

### 3.1. Data Models (C# Records)
**AppConfig**
- `Name` (Monitor Name)
- `FilePath` (Path to executable)
- `Arguments` (Startup args)
- `WatchdogEnabled` (bool)
- `TimeoutSec` (float)

**AppState**
- `IsRunning` (bool)
- `IsResponding` (bool)
- `Pid` (int)
- `Uptime` (TimeSpan)
- `RestartCount` (int)
- `LastEvent` (String/Enum)

**WatchdogModel** (Root)
- `Apps` (Spread<AppConfig>)
- `GlobalSettings` (Config)

### 3.2. Архитектура папок
```text
VVVVatchdog/
├── src/
│   ├── Model/          # Определения Records (AppConfig, AppState) и логика данных
│   ├── ViewModel/      # Логика представления, управление состоянием (Reactive Channels)
│   ├── View/           # UI патчи (ImGUI Widgets, Windows)
│   ├── Utils/          # XML Serializer, ProcessHelper, OSC Service
│   └── Assets/         # Иконки, дефолтные конфиги
├── docs/               # Документация (TECHNICAL_SPEC.md, README.md)
└── tests/              # Тестовые патчи для проверки логики без UI
```

## 4. Стек технологий
- **Платформа**: VVVV Gamma
- **UI**: VL.ImGUI
- **Logic**: C# Records, Reactive Extensions (Observable/Channels), MVVM Pattern
- **IO**: VL.IO.OSC
