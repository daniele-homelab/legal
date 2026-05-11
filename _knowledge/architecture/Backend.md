---
type: architecture
title: Backend
component: time-ledger-py
tags:
  - architecture
  - component/be
updated: 2026-04-28
---

# Backend

> [!info] Doc di codice/build
> README tecnico (build, run, test, deploy) in `time-ledger-py/README.md`.

## Stack

| Layer | Tecnologia |
|-------|-----------|
| Runtime | Python 3.11+ |
| Framework | FastAPI (ASGI) |
| Server | Gunicorn + Uvicorn workers |
| ORM | SQLAlchemy 2 (mapped_column, Mapped) |
| Migrazioni | Alembic |
| Config | Pydantic-settings (env vars / `.env`) |
| Auth | JWT locale — JWKS da Zitadel (cachato) |
| Metrics | prometheus-client — middleware HTTP + collector DB |
| Logging | structlog / logging standard, configurabile via `LOG_LEVEL` |

## Struttura app

```
app/
├── main.py              ← FastAPI app, middleware, router registration
├── core/
│   ├── config.py        ← Settings (Pydantic), URL DB, JWKS URL
│   ├── auth.py          ← Validazione JWT, caching JWKS, estrazione ruoli
│   ├── metrics.py       ← PrometheusMiddleware, register_db_collector
│   └── logging_config.py
├── db/
│   ├── base.py          ← DeclarativeBase
│   └── session.py       ← SessionLocal factory
├── models/              ← 6 ORM models (vedi [[Data-model]])
├── api/                 ← Router + schema Pydantic per ogni dominio
└── services/
    └── dashboard_service.py  ← logica widget, straordinari, accrual
```

## Endpoint principali

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| GET | `/api/health` | — | Liveness check |
| GET | `/api/health/db` | — | DB connectivity check |
| PUT | `/api/me` | user | Upsert profilo utente (da claims ID token) |
| GET | `/api/entry-types` | user | Lista tipi di voce attivi |
| POST | `/api/entry-types` | admin | Crea tipo |
| PATCH | `/api/entry-types/{key}` | admin | Aggiorna tipo |
| DELETE | `/api/entry-types/{key}` | admin | Disattiva tipo |
| GET | `/api/time-entries` | user | Lista voci (filtri: date, type, user_id per admin) |
| POST | `/api/time-entries` | user | Crea voce |
| GET | `/api/time-entries/{id}` | user | Dettaglio voce |
| PATCH | `/api/time-entries/{id}` | user | Aggiorna voce |
| DELETE | `/api/time-entries/{id}` | user | Elimina voce |
| GET | `/api/dashboard/widgets` | user | Configurazione widget dashboard |
| PUT | `/api/dashboard/widgets` | user | Salva configurazione widget |
| GET | `/api/dashboard/overtime-settings` | user | Impostazioni calcolo straordinari |
| PUT | `/api/dashboard/overtime-settings` | user | Salva impostazioni straordinari |
| GET | `/api/dashboard/overtime` | user | Calcolo straordinari periodo |
| GET | `/api/dashboard/accrual` | user | Lista accrual config |
| PUT | `/api/dashboard/accrual/{key}` | user | Upsert accrual config + rate |
| DELETE | `/api/dashboard/accrual/{key}` | user | Elimina accrual config |
| GET | `/api/admin/users` | admin | Lista utenti (paginata, ricercabile) |
| GET/PUT | `/api/admin/users/{sub}/widgets` | admin | Widget di un utente |
| GET/PUT/DELETE | `/api/admin/users/{sub}/accrual/{key}` | admin | Accrual di un utente |
| GET | `/metrics` | — | Prometheus scrape endpoint |

## Dipendenze esterne

- **PostgreSQL** `postgres.applications.svc.cluster.local:5432` — database principale
- **Zitadel** `http://zitadel.applications.svc.cluster.local:8080/oauth/v2/keys` — JWKS per validazione JWT (URL interno al cluster, bypassa Cloudflare)

## Variabili di configurazione

| Var | Tipo | Note |
|-----|------|------|
| `ENV` | str | `local` / `production` — in production richiede `ZITADEL_API_AUDIENCE` |
| `DB_HOST/PORT/NAME/USER/PASSWORD` | str | Connessione PostgreSQL |
| `ZITADEL_DOMAIN` | str | es. `auth.daniele-dev.com` |
| `ZITADEL_CLIENT_ID` | str | Client ID Zitadel (in Secret k8s) |
| `ZITADEL_API_AUDIENCE` | str | Audience claim, obbligatorio in produzione (in Secret k8s) |
| `ZITADEL_JWKS_URL` | str? | Override JWKS URL — in cluster punta all'IP interno |
| `CORS_ORIGINS` | list[str] | `["https://time-ledger.daniele-dev.com","https://time-ledger.serben.localdomain"]` |
| `LOG_LEVEL` | str | default `warning` |
| `DOCS_ENABLED` | bool | Swagger/ReDoc, default `false` in produzione |
