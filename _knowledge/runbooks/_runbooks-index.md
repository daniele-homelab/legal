---
type: moc
title: Runbooks Index
---

# Runbooks

```dataview
TABLE WITHOUT ID
  file.link AS "Runbook",
  trigger AS "Quando usarlo"
FROM "_knowledge/runbooks"
WHERE type = "runbook"
SORT file.name ASC
```
