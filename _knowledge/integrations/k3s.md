---
type: integration
title: Deployment su k3s
target: k3s-cluster
tags:
  - integration
  - deployment
updated: 2026-04-28
---

# Deployment su k3s

> [!info] Cross-vault
> Per dettagli cluster-side vedi vault separato `E:\Workspaces\IdeaProjects\k3s`. Riferimenti cross-vault usano link `file:///` perché Obsidian non risolve `[[ ]]` tra vault diversi.
>
> Punti di ingresso:
> - [k3s vault — Index](file:///E:/Workspaces/IdeaProjects/k3s/00-Index.md)
> - [k3s-monitoring → Prometheus (scrapa time-ledger:8000)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-monitoring/docs/prometheus/_index.md)
> - [k3s-applications → cloudflared (tunnel `time-ledger.daniele-dev.com` → traefik)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-applications/docs/cloudflared/_index.md)
> - [k3s-applications → postgresql (DB condiviso)](file:///E:/Workspaces/IdeaProjects/k3s/k3s-applications/docs/postgresql/_index.md)

## Namespace

`time-ledger` — definito in `time-ledger-py/k8s/base/namespace.yaml` e impostato come default in ogni `kustomization.yaml`.

## Componenti deployati

| Componente | Tipo k8s | Replicas | Image | Porta |
|------------|----------|----------|-------|-------|
| `time-ledger` (BE) | Deployment | 1 | `registry.serben.localdomain:5000/time-ledger-py:v2.0.1` | 8000 |
| `time-ledger-fe` (FE) | Deployment | 1 | `registry.serben.localdomain:5000/time-ledger-fe:v1.0.5` | 8080 |
| `postgres` (DB) | StatefulSet (ns `applications`) | — | condiviso — vedi k3s-applications | 5432 |

> [!note]
> Il database NON è nel namespace `time-ledger`. Risiede nel namespace `applications` (k3s-applications bundle). L'app BE lo raggiunge via DNS interno: `postgres.applications.svc.cluster.local:5432`.

## Ingress

| Host | Path | Backend | Entrypoint Traefik | Note |
|------|------|---------|-------------------|------|
| `time-ledger.serben.localdomain` | `/` | `time-ledger-fe:80` | `web` | LAN only |
| `time-ledger.serben.localdomain` | `/api` | `time-ledger:8000` | `web` | LAN only (internal ingress) |
| `time-ledger.daniele-dev.com` | `/` | `time-ledger-fe:80` | `web` | via Cloudflare Tunnel |
| `time-ledger.daniele-dev.com` | `/api` | `time-ledger:8000` | `web` | via Cloudflare Tunnel |

Il backend ha anche un `ingress-internal.yaml` con certificato interno (`certificate-internal.yaml`) per accesso HTTPS sulla LAN.

Traefik seleziona il path `/api` (più specifico) rispetto a `/` — il frontend e il backend condividono lo stesso hostname.

## Secrets / config

I secret sono creati **manualmente** sul cluster (non in GitOps — nessun Sealed Secrets o SOPS attualmente).

```bash
kubectl -n time-ledger create secret generic time-ledger-secret \
  --from-literal=DB_NAME='time_ledger' \
  --from-literal=DB_USER='time_ledger_user' \
  --from-literal=DB_PASSWORD='<password>' \
  --from-literal=ZITADEL_CLIENT_ID='<client-id>' \
  --from-literal=ZITADEL_API_AUDIENCE='<audience>'
```

| Chiave Secret | Descrizione |
|---------------|-------------|
| `DB_NAME` | Database PostgreSQL |
| `DB_USER` | Utente PostgreSQL |
| `DB_PASSWORD` | Password PostgreSQL |
| `ZITADEL_CLIENT_ID` | Client ID app Zitadel |
| `ZITADEL_API_AUDIENCE` | Audience claim JWT (obbligatorio in produzione) |

La ConfigMap `time-ledger-config` contiene le variabili non sensibili: `ENV`, `DB_HOST`, `ZITADEL_DOMAIN`, `ZITADEL_JWKS_URL` (URL interno), `CORS_ORIGINS`, `LOG_LEVEL`, etc.

## Manifest / Helm chart

Kustomize puro — nessun Helm chart.

| Componente | Path manifest |
|------------|--------------|
| Backend | `time-ledger-py/k8s/base/` + `overlays/local/` |
| Frontend | `time-ledger-fe/k8s/base/` + `overlays/local/` |

Fleet non gestisce direttamente time-ledger — i manifest vengono applicati tramite `kubectl apply -k` o integrati nel bundle `k3s-applications` (da verificare).

## Osservabilità

- **Prometheus** scrapa `/metrics` su `time-ledger.time-ledger.svc.cluster.local:8000` ogni 30s
- **Grafana** dashboard dedicata: `k3s-monitoring/grafana/dashboards/time-ledger.json`
- Metriche esposte: latenza HTTP per endpoint, contatori request, pool DB connections

## Componenti collegati

- [[../architecture/Frontend|time-ledger-fe (architettura)]]
- [[../architecture/Backend|time-ledger-py (architettura)]]
- [[../architecture/Data-model|Data model]]
