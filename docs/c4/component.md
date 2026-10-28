# C4 · Уровень 3 — Components

Внутреннее устройство ядра `createAsyncAiTask` (целевая архитектура). Порты
(`Transport` / `Persistence`) отделяют доменную политику от инфраструктуры.
Ключевое отличие от текущего: **timeout** — явное терминальное состояние, а не
молчаливый дроп задачи через 7 минут.

```mermaid
flowchart TB
  subgraph orch["createAsyncAiTask"]
    sm["State Machine<br/>waiting → processing → done | failed | timeout | canceled"]
    sched["Scheduler<br/>backoff + jitter (конфиг, не хардкод)"]
    retry["Idempotent Receiver<br/>lastSeq + поглощающий терминал"]
    prog["Progress reporter"]
    tport(["Transport port"])
    pport(["Persistence port"])
  end
  lp["long-poll adapter"]
  ws["ws-push adapter"]
  lsad["localStorage adapter (validated)"]
  srv["server job-store adapter · будущее"]
  sm --> sched
  sm --> retry
  sm --> prog
  sm --> tport
  sm --> pport
  tport --> lp
  tport --> ws
  pport --> lsad
  pport --> srv
```

Трассируемость: `Transport port` — [ADR-0002](../adr/0002-transport-port.md);
`Persistence port` — [ADR-0003](../adr/0003-task-state-store.md);
`Idempotent Receiver` — [ADR-0001](../adr/0001-delivery-guarantees.md), [ADR-0008](../adr/0008-idempotency.md).
