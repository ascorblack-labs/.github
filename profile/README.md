## ascorblack labs

AI-инфраструктура для продакшн-систем на базе LLM.

Основатель — [Александр Тихонов](https://ascorblack.ru) ([@ascorblack](https://github.com/ascorblack)), AI Systems Engineer из Санкт-Петербурга.
Специализация: агентная оркестрация, поисковая инфраструктура, backend-разработка, интеграционные API.

---

### Protocore

Protocol-first платформа для оркестрации LLM-агентов. 57 модулей ядра, 30+ типизированных протоколов, 85+ типов событий, 19 точек расширения через хуки. Работает с любой OpenAI-совместимой моделью — от GPT/Claude до локальных Qwen/Llama через vLLM/Ollama.

Сайт: https://protocore.ascorblack.ru

| Компонент | Описание |
|-----------|----------|
| **protocore** | Иммутабельный цикл оркестрации, 30+ протоколов (`typing.Protocol`), 19 хуков (pluggy), 3-уровневое сжатие контекста, 6 режимов субагентов, workflow DAG, skills, structured output, risk scoring, failure classification, result salvage, scratchpad, plan verification, runtime invariants. 4 863 теста, 96% покрытие |
| **protocore-enterprise** | Продакшн-дистрибуция: адаптеры (PostgreSQL, Redis, OpenSearch, RabbitMQ), FastAPI-сервис, RBAC (8 ролей), control plane, Access Plan (квоты, breach-политики, сервисные классы), автономный воркер (CRON/INTERVAL/IMMEDIATE, leader election), persona management, 10 аналитических API, метрики, sandbox-сайдкар, CLI, Docker Compose деплой. 2 665 тестов, 84% покрытие |
| **protocore-dashboard** | Админ-панель: агенты, маршрутизация, сессии, трейсы, RBAC, навыки, шаблоны, Access Plans, автономные задачи, workspace-политики, аналитика (Next.js 15 / React 19) |
| **protocore-chat** | Чат-приложение с invite-only авторизацией, SSE-стримингом, approval workflow, визуализацией tool calls и артефактов, лендинг-страницей (Next.js 15 / React 19) |
| **protocore-live-eval** | Фреймворк для live-оценки агентов: сценарии, скоринг, параллельный запуск, CLI |

#### Архитектура

```
protocore (core)             — чистое ядро, без зависимостей от бэкендов
  ├── protocore_tools        — composable tool handlers (web_fetch, web_search)
  └── protocore-enterprise   — адаптеры, сервисный слой, деплой
        ├── protocore-dashboard  — админ-UI
        ├── protocore-chat       — пользовательский чат + лендинг
        └── protocore-live-eval  — оценка качества агентов
```

Зависимости строго сверху вниз: `protocore` → `protocore_adapters` → `protocore_service` → `protocore_distributions`. Фронтенды общаются только через HTTP/SSE.

#### Стек

Python 3.12+ / FastAPI / Pydantic v2 / asyncio / PostgreSQL / Redis / OpenSearch / RabbitMQ / Docker / Next.js 15 / React 19 / TypeScript

#### Ключевые возможности ядра

- **Иммутабельный цикл оркестрации** с тремя бюджетами (итерации, tool calls, токены) и graceful degradation
- **Протокол-ориентированная архитектура** — 30+ typed протоколов для LLM, tools, state, transport, telemetry
- **6 режимов субагентов**: LEADER, AUTO_SELECT, PARALLEL, TOOL_ORCHESTRATED, CLI_NATIVE, BYPASS
- **3-уровневое сжатие контекста** (micro/auto/manual) с identity reinjection и аварийной компрессией
- **Прогрессивная загрузка инструментов** (BM25-retrieval) — экономия до 40% токенов
- **KV-кэш оптимизация** — снижение стоимости повторных запросов до 10x
- **Hallucination Override** — проверка ответов модели на соответствие фактическим результатам
- **Классификация ошибок** (7 категорий) с per-category стратегиями + ResultSalvage
- **Circuit breaker** с LLM-диагностикой для субагентов
- **CollapseDetector** (Jaccard similarity) + **DriftDetector** (семантический уход от задачи)
- **Risk scoring**, task constraints, tool preconditions, runtime invariants
- **Scratchpad** — append-only общая память с LLM-консолидацией
- **Worktree isolation** для параллельных агентов + ConflictDetector
- **Shell safety** (~25 deny-паттернов) + **WorkspaceApprovalPolicy** per operation class
- **19 хуков** через pluggy, **85+ типов событий**, **100+ Pydantic-моделей**

#### Ключевые возможности enterprise

- **LLM-маршрутизация**: RoutedLLMClient, circuit breaker, health monitoring, квоты, fallback targets
- **Enterprise RBAC**: 8 ролей (viewer → control_plane_admin), scoped bindings
- **Access Plan**: коммерческие тарифы с квотами, breach-политиками, сервисными классами
- **Очередь задач**: RabbitMQ с приоритизацией, dead-letter, consumer pools
- **Автономный воркер**: Postgres leader election, CRON/INTERVAL/IMMEDIATE триггеры, архивация
- **10 аналитических API**: usage, cost, latency, errors, activity
- **SSE-стриминг**: bootstrap/replay, reconnection, rate limiting, 12+ envelope types
- **Sandbox-сайдкар**: изолированное выполнение кода с Chromium
- **Persona profiles**: секционированные профили с template-переменными
- **Per-run метрики**: input/output/cached/reasoning tokens, cost estimate, Prometheus-экспорт

#### Качество кода

| Метрика | Core | Enterprise |
|---------|------|------------|
| Тесты | 4 863 | 2 665 |
| Покрытие | 96% | 84% |
| Порог | ≥96% | ≥80% |
| Линтер | ruff | ruff |
| Типы | mypy strict | mypy strict |
| Безопасность | bandit, pip-audit | bandit, pip-audit |

**Итого: 7 500+ тестов** across core + enterprise.

#### Текущий статус

Активная разработка. Полный стек развернут: backend, админ-панель, чат-клиент, eval-фреймворк, sandbox, автономный воркер. По результатам независимых аудитов Protocore архитектурно превосходит OpenAI Agents SDK, Claude Code CLI, Swarms и Litmus по надёжности, экономичности и контролю.

---

### Контакты

[ascorblack.ru](https://ascorblack.ru) / [Telegram](https://t.me/notsoulmate) / a@scorblack.ru
