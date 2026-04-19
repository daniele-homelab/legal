# Project docs template

Copia questa cartella **dentro un progetto** come `docs/`:

```bash
cp -r _templates/project-docs ../time-ledger-fe/docs
cp -r _templates/project-docs ../time-ledger-py/docs
```

Poi committa nel repo del singolo progetto. Il vault Obsidian la vedrà automaticamente.

## Convenzioni

- `_index.md` con `type: project` → appare nei dashboard del vault
- Wikilinks relativi: `[[../_index]]`, `[[../../_knowledge/architecture/Overview]]`
