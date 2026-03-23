## ascorblack labs

AI-инфраструктура для продакшн-систем на базе LLM.

Основатель — [Александр Тихонов](https://ascorblack.ru) ([@ascorblack](https://github.com/ascorblack)), AI Systems Engineer из Санкт-Петербурга.
Специализация: агентная оркестрация, поисковая инфраструктура, backend-разработка, интеграционные API.

---

### Protocore

Protocol-first платформа для оркестрации LLM-агентов. Предсказуемый runtime, event-driven наблюдаемость, безопасное исполнение инструментов.

| Компонент | Описание |
|-----------|----------|
| **protocore** | Иммутабельный цикл оркестрации, контракты через `typing.Protocol`, система хуков, сжатие контекста, субагенты, workflow DAG, skills, structured output. 2000+ тестов, 97%+ покрытие |
| **protocore-enterprise** | Продакшн-дистрибуция: адаптеры (PostgreSQL, Redis, MongoDB), FastAPI-сервис, RBAC, control plane, persona management, метрики, CLI, Docker Compose деплой |
| **protocore-dashboard** | Админ-панель для управления агентами, маршрутизацией, сессиями, трейсами, RBAC, навыками и шаблонами (Next.js 15 / React 19) |
| **protocore-chat** | Пользовательское чат-приложение с invite-only авторизацией, SSE-стримингом, human-in-the-loop и визуализацией tool calls (Next.js 15 / React 19) |
| **protocore-live-eval** | Фреймворк для live-оценки агентов: сценарии, скоринг, параллельный запуск, CLI |

#### Архитектура

```
protocore (core)             — чистое ядро, без зависимостей от бэкендов
  └── protocore-enterprise   — адаптеры, сервисный слой, деплой
        ├── protocore-dashboard  — админ-UI
        ├── protocore-chat       — пользовательский чат
        └── protocore-live-eval  — оценка качества агентов
```

#### Стек

Python 3.12+ / FastAPI / Pydantic v2 / asyncio / PostgreSQL / Redis / MongoDB / Docker / Next.js 15 / React 19 / TypeScript

#### Ключевые возможности

- Иммутабельный цикл оркестрации с бюджетными лимитами (итерации, tool calls, токены)
- Протокол-ориентированная архитектура — LLM, tools, state, transport, telemetry через адаптеры
- Субагентная оркестрация: LEADER, AUTO_SELECT, PARALLEL, BYPASS, TOOL_ORCHESTRATED
- Workflow DAG — граф-ориентированное исполнение сценариев
- 3-уровневое сжатие контекста (micro / auto / manual) с LLM-суммаризацией
- Skills — динамическая загрузка и каталог навыков
- Thinking controls — профили рассуждений с бюджетами токенов
- Shell safety и worktree isolation — безопасное исполнение команд
- SSE-стриминг с approval workflow (human-in-the-loop)
- RBAC с гранулярными скоупами и управление персонами
- Оптимизирован для локальных моделей (vLLM + Qwen 3/3.5)

#### Текущий статус

Активная разработка. Ядро стабильно (2000+ тестов, 97%+ покрытие), enterprise-слой — 80%+ покрытие. Полный стек развернут: backend, админ-панель, чат-клиент, eval-фреймворк.

---

### Контакты

[ascorblack.ru](https://ascorblack.ru) / [Telegram](https://t.me/notsoulmate) / a@scorblack.ru
