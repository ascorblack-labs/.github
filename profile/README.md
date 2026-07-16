<div align="center">

<picture>
  <img alt="Protocore — protocol-first runtime for stateful AI agents" src="https://raw.githubusercontent.com/ascorblack-labs/.github/main/profile/assets/hero.svg" width="100%">
</picture>

<br/>

**Protocol-first runtime · Stateful agent systems · Reproducible research**

<br/>

[![Site](https://img.shields.io/badge/site-protocore.ascorblack.com-10b981?style=for-the-badge&labelColor=0d1117)](https://protocore.ascorblack.com)
[![Research](https://img.shields.io/badge/research-publications-06b6d4?style=for-the-badge&labelColor=0d1117)](https://protocore.ascorblack.com/research/)
[![Telegram](https://img.shields.io/badge/contact-Telegram-26a5e4?style=for-the-badge&labelColor=0d1117&logo=telegram&logoColor=white)](https://t.me/notsoulmate)

</div>

---

## Protocore

Protocore — разрабатываемая Ascorblack Labs система для построения и эксплуатации stateful AI-агентов. В её основе находится независимое Python-ядро с типизированными контрактами, потоковым циклом выполнения и явными границами между оркестрацией, инфраструктурными адаптерами, сервисами и пользовательскими интерфейсами.

Система охватывает полный жизненный цикл агентного запуска: сессии и состояние, вызовы инструментов, потоковую доставку событий, подтверждение рискованных действий, восстановление выполнения, фоновые задачи, изолированное исполнение и наблюдаемость. Подключения к моделям и внешним системам реализуются через адаптеры и сервисные контракты.

Исходный код Protocore распространяется на условиях **проприетарных лицензий**. Публичные материалы проекта, архитектурные статьи и исследовательские публикации собраны на [protocore.ascorblack.com](https://protocore.ascorblack.com).

## Архитектура

```mermaid
flowchart TB
    UI["Пользовательские интерфейсы<br/>chat · dashboard"]
    API["protocore-enterprise<br/>API · streaming · policies · adapters"]
    Core["protocore<br/>protocol-first agent runtime"]
    Auto["protocore-autonomous<br/>фоновые и периодические задачи"]
    Sandbox["protocore-sandbox<br/>изолированное исполнение"]
    Tools["Dynamic Tools<br/>tools · SDK · MCP service"]
    Data["Хранилища и внешние системы"]
    Models["Model providers"]

    UI --> API
    Auto --> API
    API --> Core
    API --> Sandbox
    API --> Tools
    API --> Data
    Core --> Models
```

Ключевое ограничение зависимости: чистое ядро не импортирует service-слой или frontend-код. Enterprise-слой связывает runtime с хранилищами и сервисами; интерфейсы общаются с backend по HTTP и потоковым API.

## Репозитории

Разработка разделена по явным зонам ответственности:

| Репозиторий | Стек | Назначение |
| --- | --- | --- |
| `protocore` | Python 3.12+ · Pydantic | Чистое ядро оркестрации: цикл выполнения, контракты, состояние сессии, hooks и runtime invariants. |
| `protocore-enterprise` | Python 3.12+ · FastAPI | Service-слой: API, аутентификация и политики, адаптеры данных, streaming и интеграция runtime. |
| `protocore-autonomous` | Python 3.12+ · FastAPI | Сервис автономных, отложенных и периодических агентных задач. |
| `protocore-sandbox` | Python 3.12+ · FastAPI | Control plane для изолированных сред выполнения. |
| `protocore-chat` | React 19 · Vite · Hono | Пользовательский чат, Hono BFF, потоковые ответы, tool calls и артефакты. |
| `protocore-dashboard` | Next.js 15 · React 19 | Администрирование runtime, агентов, инструментов, сессий и политик. |
| `protocore-tools` | Python 3.12+ | Authoring-репозиторий и delivery workflow для Dynamic Tools. |
| `protocore-tools-sdk` | Python 3.12+ · Pydantic | Минимальные типизированные контракты для авторов инструментов. |
| `protocore-tools-mcp` | Python 3.12+ · FastAPI · MCP | Публикация, хранение и выполнение версий динамических инструментов. |
| `protocore-infra` | Docker Compose | Локальная и сервисная инфраструктура данных. |
| `protocore-platform-gitlab` / `protocore-platform-harbor` | Helm | Самостоятельные platform-компоненты для GitLab и registry. |

Репозитории проекта ведутся приватно. Таблица описывает границы компонентов, а не перечень публичных пакетов.

## Runtime

- **Protocol-first contracts.** Интеграции runtime определяются типизированными интерфейсами, а инфраструктурные реализации находятся за пределами ядра.
- **Stateful execution.** Сессия, workspace, артефакты и состояние выполнения сохраняются между шагами и могут быть восстановлены после прерывания.
- **Streaming by design.** Текстовые дельты и runtime-события передаются пользовательским интерфейсам по мере появления.
- **Three-layer tool surface.** Ядро предоставляет встроенные инструменты, enterprise добавляет сервисные возможности, Dynamic Tools подключаются во время работы через отдельный delivery и MCP-контур.
- **Explicit control boundaries.** Approval policies, ограничения workspace и изолированное выполнение отделяют решение модели от фактического воздействия на внешние системы.
- **Observable execution.** События, tool calls, usage и состояние запуска доступны service-слою для диагностики и аналитики.

## Проверяемая инженерная база

По состоянию на **16 июля 2026 года**:

| Проверка | Результат |
| --- | ---: |
| Собранные тесты `protocore` | 2 310 |
| Собранные тесты `protocore-enterprise` | 5 128 |
| Branch coverage `protocore` | 91% |

Это датированный snapshot локальной верификации, а не постоянно обновляемая метрика. Актуальные результаты и методики публикуются вместе с соответствующими материалами проекта.

## Исследования

Исследовательская работа Protocore посвящена измеримому поведению tool-using agents и runtime-механизмов, а не только итоговым ответам модели. Текущие направления публикаций:

- runtime-driven convergence при генерации ограниченных по длине артефактов;
- artifact-grounded evaluation агентных систем;
- protocol-first архитектура stateful agent runtime.

Статьи, экспериментальные материалы и PDF-версии публикуются в разделе [Research](https://protocore.ascorblack.com/research/). Каждая численная оценка в публикациях сопровождается описанием методики и границ применимости.

## Технологии

![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Pydantic](https://img.shields.io/badge/Pydantic-v2-E92063?style=flat-square&logo=pydantic&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?style=flat-square&logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=flat-square&logo=vite&logoColor=white)
![Hono](https://img.shields.io/badge/Hono-E36002?style=flat-square&logo=hono&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-15-000000?style=flat-square&logo=next.js&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)

## Контакты

**Основатель — [Александр Тихонов](https://ascorblack.ru)** ([@ascorblack](https://github.com/ascorblack)), AI Systems Engineer из Санкт-Петербурга.

[![Site](https://img.shields.io/badge/ascorblack.ru-10b981?style=flat-square)](https://ascorblack.ru)
[![Email](https://img.shields.io/badge/a@scorblack.ru-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:a@scorblack.ru)
[![Telegram](https://img.shields.io/badge/@notsoulmate-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/notsoulmate)

---

<div align="center">
<sub>© 2026 Ascorblack Labs · Все права защищены</sub>
</div>
