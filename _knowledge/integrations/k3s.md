---
type: integration
title: Deployment su k3s
target: k3s-cluster
updated: 2026-04-19
---

# Deployment su k3s

> Riferimenti per come time-ledger gira sul cluster k3s. Per i dettagli cluster-side vedi il vault separato `E:\Workspaces\IdeaProjects\k3s`.

## Namespace

<!-- es. time-ledger -->

## Componenti deployati

| Componente | Tipo k8s | Replicas | Image |
|------------|----------|----------|-------|
| time-ledger-fe | Deployment | | |
| time-ledger-py | Deployment | | |
| postgres | StatefulSet | | |

## Ingress

| Componente | Host | TLS |
|------------|------|-----|
| FE | <!-- es. time-ledger.home.lan --> | |
| API | <!-- es. api.time-ledger.home.lan --> | |

## Secrets / config

<!-- Quali secret servono, dove sono gestiti (sealed-secrets / SOPS) -->

## Manifest / Helm chart

<!-- Path nel repo k3s-applications o link al chart -->

## Vault k3s — riferimenti utili

- File system: `E:\Workspaces\IdeaProjects\k3s`
- Apri come vault separato in Obsidian per consultare runbook cluster-side
