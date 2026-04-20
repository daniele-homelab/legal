---
type: project
title:               # nome leggibile (richiesto per Front Matter Title)
aliases: []
tags:
  - component
  - product/time-ledger
component:           # time-ledger-fe | time-ledger-py
language:            # TypeScript | Python | ...
status: planned      # planned | dev | prod
runtime:
framework:
deployed_on: k3s
repo:
updated: 2026-04-19
---

# <nome componente>

> [!info] In una riga
> Cosa fa questo componente.

## Diagramma

```mermaid
flowchart LR
  user((User)) --> fe[FE]
  fe --> api[API]
  api --> db[(DB)]
```

## Doc collegate

- [[architecture]]
- [[development]]
- [[testing]]
- [[runbooks/local-setup]]

## Stack

(versioni runtime, framework, librerie principali)

## Comandi rapidi

```bash
# es:
# npm run dev    # FE
# uvicorn ...    # BE
# npm test / pytest
# npm run build
```

## Riferimenti vault

- Architettura prodotto: [[../../_knowledge/architecture/_index]]
- Deploy cluster: [[../../_knowledge/integrations/k3s]]
