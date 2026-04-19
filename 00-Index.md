---
type: moc
title: time-ledger — Index
---

# time-ledger

## Architettura

- [[_knowledge/architecture/Overview|Overview]]
- [[_knowledge/architecture/Frontend|Frontend (FE)]]
- [[_knowledge/architecture/Backend|Backend (Py)]]
- [[_knowledge/architecture/Data-model|Data model]]

## Componenti

```dataview
TABLE WITHOUT ID
  file.link AS "Componente",
  language AS "Stack",
  status AS "Status"
FROM "time-ledger-fe" OR "time-ledger-py"
WHERE type = "project"
```

## Integrazioni

- [[_knowledge/integrations/k3s|Deployment su k3s]]

## Decisioni (ADR)

```dataview
TABLE WITHOUT ID
  file.link AS "ADR",
  date AS "Data",
  status AS "Stato"
FROM "_knowledge/decisions"
WHERE type = "decision"
SORT date DESC
```

## Runbook

```dataview
LIST
FROM "_knowledge/runbooks"
WHERE type = "runbook"
SORT file.name ASC
```

## Modifiche recenti

```dataview
TABLE WITHOUT ID
  file.link AS "Nota",
  file.mtime AS "Modificata"
FROM "" AND -"_templates"
WHERE file.mtime >= date(today) - dur(7 days)
SORT file.mtime DESC
LIMIT 15
```
