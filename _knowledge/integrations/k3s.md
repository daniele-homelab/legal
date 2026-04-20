---
type: integration
title: Deployment su k3s
target: k3s-cluster
tags:
  - integration
  - deployment
updated: 2026-04-19
---

# Deployment su k3s

> [!info] Cross-vault
> Per dettagli cluster-side vedi vault separato `E:\Workspaces\IdeaProjects\k3s`. Riferimenti cross-vault usano link `file:///` perche Obsidian non risolve `[[ ]]` tra vault diversi.
>
> Punti di ingresso:
> - [k3s vault — Index](file:///E:/Workspaces/IdeaProjects/k3s/00-Index.md)
> - [k3s-monitoring → Prometheus (scrapa time-ledger:8000)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-monitoring/docs/prometheus/_index.md)
> - [k3s-applications → cloudflared (tunnel `time-ledger.daniele-dev.com` → traefik)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-applications/docs/cloudflared/_index.md)
> - [k3s-applications → postgresql (DB condiviso)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-applications/docs/postgresql/_index.md)

## Namespace

(es. `time-ledger`)

## Componenti deployati

| Componente | Tipo k8s | Replicas | Image |
|------------|----------|----------|-------|
| time-ledger-fe | Deployment |  |  |
| time-ledger-py | Deployment |  |  |
| postgres | StatefulSet |  |  |

## Ingress

| Componente | Host | TLS |
|------------|------|-----|
| FE | (es. `time-ledger.home.lan`) |  |
| API | (es. `api.time-ledger.home.lan`) |  |

## Secrets / config

(quali secret servono, dove sono gestiti — sealed-secrets / SOPS)

## Manifest / Helm chart

(path nel repo k3s-applications o link al chart)

## Componenti collegati

- [[../architecture/Frontend|time-ledger-fe (architettura)]]
- [[../architecture/Backend|time-ledger-py (architettura)]]
- [[../architecture/Data-model|Data model]]
