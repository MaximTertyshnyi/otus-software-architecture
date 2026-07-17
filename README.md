# Async AI Task Orchestration Platform — архитектурная документация

Сквозной проект курса **OTUS Software Architecture**.

Клиентская платформа оркестрации асинхронных AI-задач: единая job-модель с
гарантиями доставки и сменным транспортом (long-poll / WebSocket), заменяющая
две продублированные стейт-машины и разрозненные механизмы ожидания AI-ответа.

> Доменная основа — модуль TASS в монорепозитории Selene (Aviasales):
> `ai_group_query`, `create_ai_block_api`, `polling_queue`, чат на Centrifugo.
> Здесь — только архитектурная документация проекта, без продакшен-кода.

## Статус

| | |
|---|---|
| Этап | 📝 черновик документации |
| Тип проекта | клиентская платформа (frontend-only), эволюция в сторону серверного job-сервиса зафиксирована в ADR |
| Формат | Markdown + [MADR](https://adr.github.io/madr/) + mermaid (рендерятся на GitHub) |

## Навигация

- [Контекст и границы](docs/01-context.md) — задача, границы системы, глоссарий
- [ASR](docs/02-asr.md) — architecturally significant requirements
- **C4-модель**
  - [Уровень 1 — Context](docs/c4/context.md)
  - [Уровень 2 — Containers](docs/c4/container.md) (как сейчас → целевая)
  - [Уровень 3 — Components](docs/c4/component.md)
- **ADR** (`docs/adr/`)
  - [0000 — шаблон MADR](docs/adr/0000-template.md)
  - [0001 — гарантии доставки на клиенте](docs/adr/0001-delivery-guarantees.md)
  - [0002 — транспорт: единый порт vs два движка](docs/adr/0002-transport-port.md)
  - [0003 — хранилище состояния задач](docs/adr/0003-task-state-store.md)

## Как вести документацию

- Новое архитектурное решение → новый файл в `docs/adr/` по шаблону `0000-template.md`,
  со следующим номером. Решения **не редактируются задним числом**: если решение
  изменилось — заводится новый ADR со статусом, вытесняющим старый (`superseded by`).
- Диаграммы — в mermaid прямо внутри `.md` (GitHub рендерит их сам). Экспортированные
  картинки, если понадобятся, кладём в `docs/diagrams/`.
