---
type: architecture
title: time-ledger Overview
tags:
  - architecture
updated: 2026-04-19
---

# time-ledger Overview

> [!info] Cosa è
> Cosa fa, per chi, perché esiste. (riempire con la descrizione del prodotto)

## Componenti

- **[[Frontend|time-ledger-fe]]** — (es. Next.js / React)
- **[[Backend|time-ledger-py]]** — (es. FastAPI / Flask)
- **DB** — (es. Postgres su cluster k3s)

## Diagramma

```mermaid
flowchart LR
  user((User)) --> fe[time-ledger-fe]
  fe -->|HTTP| api[time-ledger-py]
  api --> db[(Postgres)]
```

## Deploy

Vedi [[../integrations/k3s]].
