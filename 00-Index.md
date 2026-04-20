---
type: moc
title: time-ledger — Index
tags:
  - moc
---

# time-ledger

> [!info] Dashboard del prodotto
> time-ledger è un'app full-stack (FE + Python BE) deployata sul cluster k3s. Per cluster-side vedi vault separato `E:\Workspaces\IdeaProjects\k3s`.

## Architettura prodotto

- [[_knowledge/architecture/_index|Overview]]
- [[_knowledge/architecture/Frontend|Frontend]]
- [[_knowledge/architecture/Backend|Backend]]
- [[_knowledge/architecture/Data-model|Data model]]

## Componenti

> [!info] Documentazione di sviluppo
> I componenti `time-ledger-fe` e `time-ledger-py` sono repo Git separati e hanno la loro `README.md` tecnica nella root del repo (build/run/test). Le note di prodotto/architettura per entrambi vivono in [[_knowledge/architecture/_index|Architettura → Overview]].
>
> Quando saranno pronte, le note dettagliate per componente andranno in `time-ledger-fe/docs/_index.md` e `time-ledger-py/docs/_index.md`.

```dataview
TABLE WITHOUT ID
  file.link AS "Componente",
  language AS "Stack",
  framework AS "Framework",
  status AS "Status"
FROM #component
WHERE type = "project"
SORT file.folder ASC
```

## Integrazioni

- [[_knowledge/integrations/k3s|Deployment su k3s]]

## Decisioni (ADR)

> [[_knowledge/decisions/_index|Archivio decisioni]]

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

> [[_knowledge/runbooks/_index|Archivio runbook]]

```dataview
TABLE WITHOUT ID
  file.link AS "Runbook",
  trigger AS "Quando",
  parent AS "Componente"
FROM #runbook
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
