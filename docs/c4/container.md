# C4 · Уровень 2 — Containers

## Как сейчас

Два консьюмера по ~490 строк дублируют одну стейт-машину; два независимых инстанса
очереди пишут в один localStorage без валидации; чат — четвёртый, отдельный движок.

```mermaid
flowchart TB
  subgraph spa["Selene SPA · TASS"]
    agq["ai_group_query.model<br/>~499 строк · своя стейт-машина"]
    cab["create_ai_block_api.model<br/>~490 строк · своя стейт-машина"]
    pqa["polling_queue · инстанс A"]
    pqb["polling_queue · инстанс B"]
    chat["chat_transport<br/>отдельный WS-движок"]
  end
  ls[("localStorage<br/>rtRecord пустой — без валидации")]
  guides["Backend: группы"]
  tassbe["Backend: блоки"]
  centr["Centrifugo"]
  agq --> pqa --> ls
  cab --> pqb --> ls
  agq -->|"poll"| guides
  cab -->|"poll"| tassbe
  chat -->|"WS"| centr
```

## Целевая

Каждый тип задачи — тонкий конфиг (~60 строк). Стейт-машина, планировщик и
персистентность — в ядре `createAsyncAiTask`. Транспорт (long-poll / ws-push) —
сменный адаптер, поэтому чат встаёт в ту же job-модель.

```mermaid
flowchart TB
  subgraph spa["Selene SPA · TASS"]
    cfgG["config: группа · ~60 строк"]
    cfgB["config: AI-блок · ~60 строк"]
    cfgC["config: чат · transport = ws-push"]
    orch["createAsyncAiTask<br/>Task Orchestrator"]
    tlp["Transport · long-poll"]
    tws["Transport · ws-push"]
    persist["Persistence adapter<br/>валидируемая схема"]
  end
  ls[("localStorage → server job store")]
  guides["Backend: группы"]
  tassbe["Backend: блоки"]
  centr["Centrifugo"]
  cfgG --> orch
  cfgB --> orch
  cfgC --> orch
  orch --> tlp
  orch --> tws
  orch --> persist --> ls
  tlp --> guides
  tlp --> tassbe
  tws --> centr
```
