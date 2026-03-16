## ascorblack labs

AI-инфраструктура для продакшн-систем на базе LLM.

Основатель — [Александр Тихонов](https://ascorblack.ru) ([@ascorblack](https://github.com/ascorblack)), AI Systems Engineer из Санкт-Петербурга.
Специализация: агентная оркестрация, поисковая инфраструктура, backend-разработка, интеграционные API.

---

### Protocore

Protocol-first ядро для оркестрации LLM-агентов. Предсказуемый runtime, event-driven наблюдаемость, безопасное исполнение инструментов.

| Компонент | Описание |
|-----------|----------|
| **protocore** | Иммутабельный цикл оркестрации, контракты через `typing.Protocol`, система хуков, сжатие контекста, субагенты, structured output. 1000+ тестов, 97%+ покрытие |
| **protocore-enterprise** | Продакшн-дистрибуция: адаптеры (PostgreSQL, Redis, MongoDB), FastAPI-сервис, RBAC, control plane, Docker Compose деплой, CLI |
| **protocore-dashboard** | Web-интерфейс для мониторинга сессий, трейсов и ранов в реальном времени (Preact) |

#### Архитектура

```
protocore (core)          — чистое ядро, без зависимостей от бэкендов
  └── protocore-enterprise  — адаптеры, сервисный слой, деплой
        └── protocore-dashboard — веб-UI для наблюдаемости
```

#### Стек

Python 3.12+ / FastAPI / Pydantic v2 / asyncio / PostgreSQL / Redis / MongoDB / Elasticsearch / Docker / Preact

#### Ключевые возможности

- Иммутабельный цикл оркестрации с бюджетными лимитами (итерации, tool calls, токены)
- Протокол-ориентированная архитектура — LLM, tools, state, transport, telemetry через адаптеры
- Субагентная оркестрация: LEADER, AUTO_SELECT, PARALLEL, BYPASS
- 3-уровневое сжатие контекста (micro / auto / manual) с LLM-суммаризацией
- Routed LLM клиент с circuit breaker, ретраями и квотами
- SSE-стриминг с bootstrap/replay
- RBAC с гранулярными скоупами
- Оптимизирован для локальных моделей (vLLM + Qwen 3/3.5)

---

### Контакты

[ascorblack.ru](https://ascorblack.ru) / [Telegram](https://t.me/notsoulmate) / a@scorblack.ru
