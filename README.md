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
