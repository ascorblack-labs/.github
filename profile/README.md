<div align="center">

<picture>
  <img alt="Protocore — Protocol-first AI agent orchestration" src="https://raw.githubusercontent.com/ascorblack-labs/.github/main/profile/assets/hero.svg" width="100%">
</picture>

<br/>

**Protocol-first платформа оркестрации AI-агентов. Self-hosted. Любая LLM. Полный контроль.**

<br/>

[![Site](https://img.shields.io/badge/site-protocore.ascorblack.ru-10b981?style=for-the-badge&labelColor=0d1117)](https://protocore.ascorblack.ru)
[![Telegram](https://img.shields.io/badge/contact-Telegram-26a5e4?style=for-the-badge&labelColor=0d1117&logo=telegram&logoColor=white)](https://t.me/notsoulmate)

</div>

---

## О проекте

**Protocore** — платформа оркестрации AI-агентов с протокольной архитектурой, разработанная как готовый продукт, а не фреймворк. Разворачивается в контуре заказчика, работает с любой OpenAI-совместимой моделью (GPT, Claude, Qwen, Llama, vLLM, Ollama, GigaChat), не привязана к одному провайдеру и не отправляет данные наружу.

По результатам независимых аудитов архитектурно превосходит OpenAI Agents SDK, Claude Code CLI, Swarms и Litmus по надёжности, экономичности и контролю — за счёт системных решений, а не отдельных оптимизаций: переопределения галлюцинаций, классификации ошибок, прогрессивной загрузки инструментов, KV-кэш-оптимизации и worktree-изоляции параллельных агентов.

<div align="center">

<table>
<tr>
  <td align="center" width="25%">
    <h2>7 500+</h2>
    <sub>тестов качества</sub>
  </td>
  <td align="center" width="25%">
    <h2>96%</h2>
    <sub>покрытие ядра</sub>
  </td>
  <td align="center" width="25%">
    <h2>57 / 30+ / 85+</h2>
    <sub>модулей · протоколов · событий</sub>
  </td>
  <td align="center" width="25%">
    <h2>100%</h2>
    <sub>self-hosted, 152-ФЗ</sub>
  </td>
</tr>
</table>

</div>

---

## Архитектура

```mermaid
flowchart TB
    classDef core    fill:#10b981,stroke:#059669,color:#ffffff,font-weight:600
    classDef adapter fill:#0d9488,stroke:#0f766e,color:#ffffff
    classDef service fill:#06b6d4,stroke:#0891b2,color:#ffffff
    classDef ui      fill:#8b5cf6,stroke:#7c3aed,color:#ffffff
    classDef ext     fill:#1f2937,stroke:#374151,color:#e5e7eb,stroke-dasharray:4 3

    User[Пользователь / Сотрудник]:::ext
    Integ[CRM · ERP · BI · API]:::ext

    Chat[protocore-chat<br/>Next.js · SSE · invite-only]:::ui
    Dash[protocore-dashboard<br/>Next.js · RBAC · Analytics]:::ui

    Ent[protocore-enterprise<br/>FastAPI · RBAC · Access Plan · 10 Analytics APIs]:::service
    Auto[protocore-autonomous<br/>CRON / INTERVAL / IMMEDIATE<br/>leader election]:::service
    Sand[protocore-sandbox<br/>k8s execution · Chromium]:::service

    Adapt[adapters<br/>Postgres · Redis · OpenSearch · RabbitMQ]:::adapter

    Core[protocore<br/>Immutable orchestration loop · 30+ Protocols<br/>19 hooks · 6 subagent modes · 3-layer compression]:::core

    LLM[LLM Providers<br/>OpenAI · Anthropic · Qwen · Llama · vLLM · Ollama]:::ext

    User --> Chat
    Integ -.REST / SSE.-> Ent
    Chat --> Ent
    Dash --> Ent
    Ent --> Adapt
    Ent --> Core
    Auto -.HTTP.-> Ent
    Ent -.lease.-> Sand
    Core --> LLM

    Deploy[protocore-deploy<br/>Helm · GitLab CI · единая точка деплоя]:::service
    Deploy -.- Ent
    Deploy -.- Auto
    Deploy -.- Sand
    Deploy -.- Chat
    Deploy -.- Dash
```

**Принцип:** зависимости строго сверху вниз — `protocore` (чистое ядро, без upward-imports) → адаптеры → service-слой → frontend (HTTP/SSE only). Граница ядра защищена тестом импорта (`tests/test_core_import_boundary.py`).

---

## Компоненты

| Репозиторий | Стек | Назначение |
|---|---|---|
| [**protocore**](https://github.com/ascorblack-labs/protocore) | Python 3.12 · Pydantic v2 | Иммутабельный цикл оркестрации, 30+ типизированных протоколов, 19 хуков (pluggy), 6 режимов субагентов, 3-уровневое сжатие контекста, structured failure classification, scratchpad, plan verification, runtime invariants. **4 863 теста, 96% покрытия.** |
| [**protocore-enterprise**](https://github.com/ascorblack-labs/protocore-enterprise) | FastAPI · asyncio | Продакшн-дистрибуция: PostgreSQL/Redis/OpenSearch/RabbitMQ адаптеры, RBAC (8 ролей), Access Plan с квотами и breach-политиками, persona management, SSE-стриминг, 10 аналитических API, метрики. **2 665 тестов, 84% покрытия.** |
| [**protocore-autonomous**](https://github.com/ascorblack-labs/protocore-autonomous) | FastAPI · asyncio | Standalone микросервис автономных задач. CRON/INTERVAL/IMMEDIATE триггеры, Postgres advisory-lock leader election, архивация, статус-стриминг через Redis pub/sub. |
| [**protocore-sandbox**](https://github.com/ascorblack-labs/protocore-sandbox) | Python 3.12 · Kubernetes | Sandbox control plane: lease/claim/release, k8s execution backend, профили рантайма, изолированное выполнение кода с Chromium для веб-задач. |
| [**protocore-dashboard**](https://github.com/ascorblack-labs/protocore-dashboard) | Next.js 15 · React 19 · TS | Админ-панель: агенты, маршрутизация, сессии, трейсы, RBAC, навыки, шаблоны, Access Plans, автономные задачи, workspace-политики, runtime constants. |
| [**protocore-chat**](https://github.com/ascorblack-labs/protocore-chat) | Next.js 15 · React 19 · TS | Чат-приложение с invite-only авторизацией, SSE-стримингом, approval workflow, визуализацией tool calls и артефактов, лендинг-страницей. |
| [**protocore-deploy**](https://github.com/ascorblack-labs/protocore-deploy) | Helm · Bash · GitLab CI | **Единственная** точка деплоя: environment overlays, release-топология, bootstrap-скрипты, registry pull secrets, rollout ordering. |

---

## Что отличает Protocore

<table>
<tr>
<td width="33%" valign="top">

#### Hallucination Override

Модель заявляет «файл создан», а все операции были отклонены политикой? Система обнаружит расхождение и **переопределит ложный ответ** реальным результатом. Ни OpenAI Agents SDK, ни Claude Code не проверяют ответы на соответствие фактам.

</td>
<td width="33%" valign="top">

#### KV-Cache + Progressive Tools

Неизменные части запроса всегда первыми, инструменты «маскируются» вместо удаления, BM25-retrieval подгружает только нужные схемы. **До 10× дешевле** на повторных запросах, **−40% токенов** против полной выгрузки.

</td>
<td width="33%" valign="top">

#### 6 режимов мульти-агентности

LEADER, AUTO_SELECT, PARALLEL, TOOL_ORCHESTRATED, CLI_NATIVE, BYPASS. Worktree-изоляция параллельных копий, ConflictDetector, общий scratchpad с LLM-консолидацией, ретрай с обратной связью лидера.

</td>
</tr>
<tr>
<td width="33%" valign="top">

#### Structured Failure Classification

7 категорий ошибок (TRANSIENT, CAPABILITY_MISMATCH, TASK_AMBIGUITY, POLICY_DENIAL, RESOURCE_EXHAUSTION, UNVERIFIED_SUCCESS, PERMANENT) — каждая со своей стратегией. **ResultSalvage** извлекает данные даже из неудачных запусков.

</td>
<td width="33%" valign="top">

#### Approval Gates + Risk Scoring

DefaultShellSafetyPolicy (~25 deny-паттернов), WorkspaceApprovalPolicy per operation class, risk scoring (5 уровней) на каждый tool call, 2FA для деструктивных операций, санитизация секретов до того, как модель их увидит.

</td>
<td width="33%" valign="top">

#### Collapse + Drift Detection

CollapseDetector по Jaccard-похожести разрушает паттерн через рандомизацию формата результатов (−15..20% частоты зацикливания). DriftDetector ловит семантический уход и возвращает фокус.

</td>
</tr>
<tr>
<td width="33%" valign="top">

#### 3-Layer Compression + Identity Reinjection

micro_compact (плейсхолдеры с anchor protection) → auto_compact (LLM-суммаризация с retry+heuristic fallback) → manual_compact. Аварийный сжим при ContextWindowExceededError с **восстановлением личности агента**.

</td>
<td width="33%" valign="top">

#### Adaptive Runtime Steering

ExecutionStateInjector показывает агенту остаток бюджета. RuntimeInvariant отслеживает нарушения контрактов в реальном времени. **100+ параметров (RuntimeConstants)** меняются через дашборд без передеплоя.

</td>
<td width="33%" valign="top">

#### Background Tasks + Forced Finalization

Долгая операция не блокирует цикл — результат доставляется перед следующим решением модели. При исчерпании бюджета — финальный LLM-вызов без инструментов для синтеза лучшего возможного ответа, ResultSalvage не теряет уже собранные данные.

</td>
</tr>
</table>

---

## Сравнение с альтернативами

| Критерий | **Protocore** | Штатный аналитик | Заказная разработка | GigaChat / YandexGPT |
|---|---|---|---|---|
| Стоимость | **от 100 тыс./мес** | от 150 тыс./мес | 3–15 млн единоразово | от 15 тыс./мес (только API) |
| Данные у вас | **Self-hosted** | да | да | облако провайдера |
| Мульти-агентность | **6 режимов** | — | зависит от ТЗ | — |
| Запуск | **1–2 недели** | найм + онбординг | 3–6 месяцев | 1 день, но только чат |
| Масштабирование | **любые задачи** | 1 человек = 1 задача | каждый кейс — доработка | только чат |
| RBAC и контроль | **8 ролей · Access Plan** | вручную | зависит от ТЗ | API-ключи |
| Аналитика | **10 API · Dashboard** | — | доплата | базовая |
| Автозапуск по расписанию | **CRON / INTERVAL** | напоминания | доплата | — |

**Архитектурно** vs OpenAI Agents SDK / Claude Code / Swarms: protocol-first вместо God-object (в Swarms — 6 175 LOC в одном классе), KV-cache и progressive tools (отсутствуют у конкурентов), hallucination override (нет ни у кого), structured failure classification (только у Protocore), 100% self-hosted и vendor-agnostic.

---

## Как работает оркестрация

<div align="center">
  <img alt="Protocore — иммутабельный цикл оркестрации" src="https://raw.githubusercontent.com/ascorblack-labs/.github/main/profile/assets/loop.svg" width="100%">
</div>

Главный режим — **LEADER**: лидер-агент видит остаток бюджета, делегирует субагентам в worktree-изолированных копиях, читает структурированные ошибки через contamination firewall и передаёт обратную связь при повторной делегации. Subagent retry с контекстом лидера превращает команду в самообучающуюся сеть — каждая попытка лучше предыдущей.

---

## Сценарии применения

<table>
<tr>
<td width="50%" valign="top">

#### Динамическая отчётность

Агент подключается к БД, собирает данные, строит графики и генерирует готовый отчёт с выводами. По запросу или по расписанию.

> *«Отчёт по продажам за март с разбивкой по регионам»* → через 3 минуты PDF с 4 графиками и аналитическими выводами.

`SQL` `Визуализация` `CRON` `Sandbox`

</td>
<td width="50%" valign="top">

#### База знаний для сотрудников

Агент отвечает на вопросы по внутренним регламентам, документам, процедурам. Новый сотрудник получает ответы за секунды, а не ищет неделями.

> *«Какой порядок согласования договоров от 500 тыс.?»* → актуальный регламент с цитатой и пошаговой инструкцией.

`RAG` `Цитаты` `Persona` `RBAC`

</td>
</tr>
<tr>
<td width="50%" valign="top">

#### Обработка обращений

Классификация входящих, извлечение данных, черновик ответа, маршрутизация. **60–70% типовых запросов** обрабатываются автоматически.

> Обращение → классификация → извлечение № заказа → поиск в CRM → черновик → на утверждение менеджеру.

`Классификация` `API` `Approval` `Маршрутизация`

</td>
<td width="50%" valign="top">

#### Чат с проверенными ответами

Чат-интерфейс, отвечающий строго по вашему контенту. Нумерованные цитаты, ссылки на источники. **Никаких галлюцинаций.**

> Образовательная платформа (330+ вузов): студенты задают вопрос → агент отвечает строго по учебникам с цитатами.

`RAG` `Цитирование` `SSE` `Сессии`

</td>
</tr>
</table>

**Отрасли:** финтех и страхование · ритейл · телеком · образование · госсектор · промышленность.

---

## Качество кода

| Метрика          | Core (`protocore`) | Enterprise (`protocore-enterprise`) |
|------------------|--------------------|-------------------------------------|
| Тесты            | **4 863**          | **2 665**                           |
| Покрытие         | **96%**            | **84%**                             |
| Минимальный порог| ≥96%               | ≥80%                                |
| Линтер           | `ruff`             | `ruff`                              |
| Типы             | `mypy --strict`    | `mypy --strict`                     |
| Безопасность     | `bandit` + `pip-audit` | `bandit` + `pip-audit`           |

**Итого: 7 500+ тестов** на ядро + enterprise. CI блокирует merge при падении любого порога.

---

## Технологический стек

![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Pydantic](https://img.shields.io/badge/Pydantic-v2-E92063?style=flat-square&logo=pydantic&logoColor=white)
![Asyncio](https://img.shields.io/badge/asyncio-1f2937?style=flat-square)
&nbsp;
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![OpenSearch](https://img.shields.io/badge/OpenSearch-005EB8?style=flat-square&logo=opensearch&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)
&nbsp;
![Next.js](https://img.shields.io/badge/Next.js-15-000000?style=flat-square&logo=next.js&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
&nbsp;
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Helm](https://img.shields.io/badge/Helm-0F1689?style=flat-square&logo=helm&logoColor=white)
![GitLab](https://img.shields.io/badge/GitLab%20CI-FC6D26?style=flat-square&logo=gitlab&logoColor=white)

---

## Текущий статус

**Активная разработка с действующей продакшн-средой.** Полный стек развёрнут и работает: backend, админ-панель, чат-клиент, sandbox, автономный воркер, единая точка деплоя через Helm + GitLab CI.

Публичная точка входа — [protocore.ascorblack.ru](https://protocore.ascorblack.ru)

---

## Команда и контакты

**Основатель — [Александр Тихонов](https://ascorblack.ru)** ([@ascorblack](https://github.com/ascorblack)), AI Systems Engineer из Санкт-Петербурга. Специализация: агентная оркестрация, поисковая инфраструктура, backend-разработка, интеграционные API.

[![Site](https://img.shields.io/badge/ascorblack.ru-10b981?style=flat-square&logoColor=white)](https://ascorblack.ru)
[![Email](https://img.shields.io/badge/a@scorblack.ru-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:a@scorblack.ru)
[![Telegram](https://img.shields.io/badge/@notsoulmate-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/notsoulmate)
[![GitHub](https://img.shields.io/badge/@ascorblack-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/ascorblack)

---

<div align="center">
<sub>© 2026 Ascorblack Labs · Все права защищены</sub>
</div>
