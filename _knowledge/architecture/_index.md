---
type: architecture
title: time-ledger Overview
tags:
  - architecture
updated: 2026-04-28
---

# time-ledger Overview

> [!info] Cosa è
> Applicazione full-stack per il tracciamento del tempo lavorativo. Permette di registrare voci di tempo (entry), configurare tipi di lavoro, visualizzare statistiche su un dashboard personalizzabile con widget e calcolo straordinari. Deployata sul cluster k3s e accessibile via Cloudflare Tunnel.

## Componenti

- **[[Frontend|time-ledger-fe]]** — Angular 21 SPA, autenticazione OIDC/PKCE via Zitadel
- **[[Backend|time-ledger-py]]** — Python 3.11 + FastAPI REST API, Prometheus metrics
- **DB** — PostgreSQL condiviso nel namespace `applications` del cluster k3s

## Diagramma

```mermaid
flowchart LR
  user((Utente)) --> tunnel["Cloudflare Tunnel\ntime-ledger.daniele-dev.com"]
  user --> lan["Traefik LAN\ntime-ledger.serben.localdomain"]
  tunnel --> traefik[Traefik Ingress]
  lan --> traefik
  traefik -->|path /| fe["time-ledger-fe\nAngular 21 · nginx · :8080"]
  traefik -->|path /api| api["time-ledger-py\nFastAPI · gunicorn · :8000"]
  api --> db[("PostgreSQL\nns: applications")]
  api -->|JWKS| zitadel["Zitadel\nauth.daniele-dev.com"]
  fe -->|PKCE| zitadel
  prom["Prometheus\nns: monitoring"] -->|scrape :8000/metrics| api
```

## Auth flow

1. Il frontend esegue Authorization Code + PKCE verso Zitadel (`auth.daniele-dev.com`)
2. L'access token JWT viene incluso in ogni richiesta al backend come `Authorization: Bearer`
3. Il backend valida la firma localmente scaricando JWKS dall'URL interno al cluster (`zitadel.applications.svc.cluster.local:8080/oauth/v2/keys`)
4. I ruoli (`admin` / user normale) vengono letti dal claim `urn:zitadel:iam:org:project:roles`

## Deploy

Vedi [[../integrations/k3s]].
