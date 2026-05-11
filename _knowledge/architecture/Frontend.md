---
type: architecture
title: Frontend
component: time-ledger-fe
tags:
  - architecture
  - component/fe
updated: 2026-05-12
---

# Frontend

> [!info] Doc di codice/build
> README tecnico (build, run, deploy) in `time-ledger-fe/README.md`.

## Stack

| Layer | Tecnologia |
|-------|-----------|
| Framework | Angular 21.2 (standalone components) |
| Auth OIDC | angular-oauth2-oidc 17 — PKCE flow |
| Icone | lucide-angular 1.0.0 |
| Reactive | RxJS 7.8 |
| HTTP | Angular `HttpClient` con interceptor JWT |
| Routing | Angular Router con guard `AuthGuard` |
| Build | Angular CLI 21.2.10, TypeScript 5.9 |
| Server | nginx (immagine Docker multi-stage) |
| Locale | `dd-mm-yyyy`, `HH:MM`, timezone `Europe/Rome` |
| Tema | Light / Dark toggle con rilevamento preferenza OS |

## Struttura app

```
src/app/
├── core/
│   ├── auth/          ← AuthService, AuthGuard, token interceptor
│   ├── api/           ← servizi HTTP per ogni dominio (entries, types, dashboard…)
│   └── models/        ← interfacce TypeScript (TimeEntry, EntryType, Widget…)
├── features/
│   ├── dashboard/     ← overview mensile, overtime card, widget personalizzabili (max 5)
│   ├── time-entries/  ← CRUD voci tempo, filtri per data/tipo/utente, paginazione 20/pag
│   ├── entry-types/   ← gestione tipi (admin only) con colour picker
│   ├── users/         ← directory utenti paginata con editor widget per utente (admin only)
│   └── admin/         ← shell admin con route guard ruolo `admin`
├── layout/
│   ├── sidebar/       ← navigazione, sezione admin, footer utente (tema + logout)
│   ├── header/        ← top bar solo mobile (hamburger + brand)
│   └── shell/         ← layout root: sidebar + header mobile + router-outlet
└── shared/            ← componenti UI riutilizzabili, pipe, direttive
```

## Design system

Tutti i token sono in `src/styles.scss` come CSS custom properties su `:root` / `[data-theme="dark"]`.

| Token | Light | Dark |
|-------|-------|------|
| `--color-primary` | `#8b5cf6` (violet-500) | invariato |
| `--color-bg` | `#fafafa` (zinc-50) | `#09090b` (zinc-950) |
| `--color-surface` | `#ffffff` | `#18181b` (zinc-900) |
| `--color-border` | `#e4e4e7` (zinc-200) | `#3f3f46` (zinc-700) |
| `--color-text` | `#18181b` (zinc-900) | `#fafafa` (zinc-50) |
| `--color-sidebar` | `#111113` | invariato (sidebar sempre dark) |

Font: **Inter** (Google Fonts, pesi 300–700). Numeri numerici usano `font-variant-numeric: tabular-nums`.

Classi globali principali: `.btn`, `.btn-primary/secondary/danger/ghost/sm/icon`, `.form-control`, `.form-label`, `.card`, `.page-header`, `.badge`, `.data-table`, `.modal`, `.spinner`, `.toggle`, `.pagination`.

## Layout shell

```
Desktop (≥ 769px):
  ┌──────────────────────────────────────────┐
  │ Sidebar (240px, always dark)             │
  │  • Logo + brand                          │
  │  • Navigation con icone Lucide           │
  │  • Sezione "Admin" (solo ruolo admin)    │
  │  • Footer: avatar / nome / tema / logout │
  ├──────────────────────────────────────────┤
  │ Main content (flex:1, padding 28px 32px) │
  └──────────────────────────────────────────┘

Mobile (≤ 768px):
  ┌──────────────────────────────────────────┐
  │ Top bar (52px, dark): ☰  Time Ledger     │
  ├──────────────────────────────────────────┤
  │ Main content (padding 20px 16px)         │
  └──────────────────────────────────────────┘
  Sidebar = overlay drawer (z-index 50), backdrop chiude
```

L'`HeaderComponent` è nascosto su desktop (`display:none`); l'informazione utente (nome, admin badge, tema, logout) vive nel footer della sidebar.

## Icone

Le icone Lucide sono registrate globalmente in `app.config.ts` tramite `LUCIDE_ICONS`:

```typescript
{ provide: LUCIDE_ICONS, useValue: new LucideIconProvider({...}), multi: true }
```

I componenti che usano icone importano `LucideAngularModule` (senza `.pick()`) e usano `<lucide-icon name="..." [size]="N">` nei template.

Per aggiungere una nuova icona: importarla da `lucide-angular` e aggiungerla all'oggetto in `app.config.ts`.

## API consumate

| Endpoint backend | Feature |
|-----------------|---------|
| `PUT /api/me` | Login — aggiorna profilo da claims ID token |
| `GET /api/entry-types` | Dashboard, Time Entries, Entry Types |
| `POST/PATCH/DELETE /api/entry-types/{key}` | Entry Types (admin) |
| `GET /api/time-entries` | Time Entries list |
| `POST/PATCH/DELETE /api/time-entries/{id}` | Time Entries CRUD |
| `GET/PUT /api/dashboard/widgets` | Dashboard widget config |
| `GET/PUT /api/dashboard/overtime-settings` | Dashboard overtime settings |
| `GET /api/dashboard/overtime` | Dashboard overtime card |
| `GET/PUT/DELETE /api/dashboard/accrual/{key}` | Accrual config |
| `GET /api/admin/users` | Users (admin) |
| `GET/PUT /api/admin/users/{sub}/widgets` | Widget editor per utente (admin) |

## Stato globale

Nessuno store centralizzato — ogni feature usa **servizi Angular** con `BehaviorSubject` / `Observable` per lo stato locale. Il token OIDC è gestito interamente da `angular-oauth2-oidc` (storage in `sessionStorage`). I ruoli vengono letti dal claim JWT e iniettati nell'`AuthService` per i guard e la visibilità dei menu.

## Configurazione ambiente

| Variabile | Local | Produzione |
|-----------|-------|-----------|
| `apiUrl` | `http://localhost:8000/api` | relativo `/api` (stesso host Ingress) |
| `zitadel.issuer` | `https://auth.daniele-dev.com` | `https://auth.daniele-dev.com` |
| `zitadel.clientId` | `364076050998689907` | stesso |
| `zitadel.redirectUri` | `http://localhost:4200/` | `https://time-ledger.daniele-dev.com/` |

> [!warning] Redirect URI locale
> Per usare l'app in locale, `http://localhost:4200/` deve essere presente nella whitelist dei redirect URI del client Zitadel.
