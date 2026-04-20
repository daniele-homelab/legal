# time-ledger — Vault Obsidian

Knowledge base del prodotto **time-ledger** (frontend + backend).

## Struttura

- **`_knowledge/`** — note prodotto: architettura, decisioni, integrazioni, runbook
- **`_templates/`** — template per nuove note
- **`time-ledger-fe/`**, **`time-ledger-py/`** — repo dei progetti (gitignorati nel vault)
  - Ognuno ha la sua cartella `docs/` con documentazione Obsidian-compatibile

## Apri il vault

Da Obsidian: *Open folder as vault* → `E:\Workspaces\IdeaProjects\time-ledger`

## Relazione col cluster k3s

time-ledger gira sul cluster k3s. Le integrazioni cluster-side (deployment, ingress, secrets) sono documentate in `_knowledge/integrations/k3s.md`, con riferimenti al vault k3s separato (`E:\Workspaces\IdeaProjects\k3s`).

## Plugin consigliati (community)

Stessa baseline del vault `k3s` (vedi `E:\Workspaces\IdeaProjects\k3s\README.md`):

- **Dataview**, **Templater**, **Folder Notes**, **Obsidian Linter**, **Breadcrumbs**, **Iconize**, **Omnisearch**, **Tag Wrangler**, **Style Settings**, **Front Matter Title**, **Extended Graph** — config condivisa, sincronizzata a mano (sotto)
- **Obsidian Git**, **Excalidraw**, **Kanban** — config locale per vault

## Sync configurazione plugin con vault `k3s`

> **Importante**: Obsidian non ha config globale. La baseline plugin (le 11 condivise sopra) vive in `.obsidian/plugins/<plugin>/data.json` di ciascun vault e **va sincronizzata a mano** dopo modifiche. Anche il `graph.json` core va tenuto allineato.

**Quando si copia**: dopo aver toccato la config di uno dei plugin condivisi, in uno dei due vault.

**Come copiare** (PowerShell, da `E:\Workspaces\IdeaProjects\`):

```powershell
# Da k3s -> time-ledger (sostituisci direzione se modifichi qui)
$plugins = @('dataview','templater-obsidian','folder-notes','obsidian-linter','breadcrumbs','obsidian-icon-folder','omnisearch','tag-wrangler','obsidian-style-settings','obsidian-front-matter-title-plugin','extended-graph')
foreach ($p in $plugins) {
  $src = "k3s\.obsidian\plugins\$p\data.json"
  $dst = "time-ledger\.obsidian\plugins\$p\data.json"
  if ((Test-Path $src) -and (Test-Path (Split-Path $dst))) { Copy-Item $src $dst -Force }
}
```

**IMPORTANT — chiudi Obsidian PRIMA della copia.** Obsidian riscrive `data.json` in modo atomico al close/save e sovrascrive il file appena copiato se aperto.

### Accortezze per config

Le accortezze per ogni plugin sono documentate nel README del vault `k3s` (sezione "Sync configurazione plugin tra vault"). In sintesi per time-ledger:

- **Iconize**: alcune folder rules del vault `k3s` (es. `_cluster/`, `runbooks/`) puntano a cartelle che qui non esistono — non e un problema, le regole restano dormienti.
- **Linter**: la regola `format-yaml-array` e configurata anche per chiavi (`endpoints`, `depends_on`) usate solo nel vault `k3s` — nessun effetto su note time-ledger che non le hanno.
- **Front Matter Title**: ogni nota deve avere `title:` nel frontmatter, altrimenti il grafo mostra il basename del file.
- **Extended Graph**: la baseline `data.json` e copiata, **shape rules incluse** (`shapeQueries`). La sezione UI "Shapes" non e esposta in v2.7.6 — modifiche solo da JSON. Mapping shape->`type` identico al vault `k3s` (vedi README la').
- Tutto il resto: copia integrale OK.

Plugin **NON da sincronizzare** (config specifica al vault, gia in `.gitignore`): Obsidian Git, Excalidraw, Kanban.
