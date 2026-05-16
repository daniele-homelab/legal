---
type: decision
title: "ADR-001: Carry-over, target percentuale e detrazione pausa nell'accrual"
date: 2026-05-16
status: accepted
tags:
  - accrual
  - widget
  - dashboard
---

# ADR-001: Carry-over, target percentuale e detrazione pausa nell'accrual

## Contesto

L'analisi delle buste paga (gennaio–marzo 2026) ha evidenziato tre limitazioni nel sistema di accrual:

1. **Residui anno precedente non gestiti**: il saldo di ferie/ROL maturato e non goduto nell'anno precedente (ferie 61,33h, ROL 51,50h da gennaio 2026) non aveva un posto dove essere inserito — l'accrual partiva sempre da zero.

2. **Target fisso vs target percentuale**: l'accordo Smart Working prevede target parziali (ferie 120h = 68% di 176h, ROL 47,60h = 70% di 68h) che variano se la busta paga cambia. Un `target_fixed` assoluto richiedeva aggiornamenti manuali; un `target_pct` si aggiusta automaticamente al maturato.

3. **Pausa pranzo inclusa nel consumato**: le voci di ferie inserite con orario (09:00–18:00) producono 540 min invece di 480 min netti, gonfiando il consumato. La stessa logica esisteva già per gli straordinari (`break_minutes`), ma non era applicata al calcolo accrual.

## Decisioni

### 1. `carry_over` su `accrual_configs`

Colonna `FLOAT NOT NULL DEFAULT 0` che rappresenta il saldo di apertura portato dall'anno precedente. Sommato al totale delle rate nella funzione `calculate_accrued()`. Il default 0 mantiene la compatibilità con le configurazioni esistenti.

### 2. `target_pct` su `dashboard_widgets`

Colonna `FLOAT NULL` con check `0 < x <= 1`. Disponibile solo in modalità `direction=remaining / targetType=accrual`. A runtime `target_effective = accrued × target_pct` viene calcolato (non persistito) e restituito in `WidgetComputed`. Il widget mostra `target_effective − consumed` come valore principale e usa `consumed / target_effective` per la progress bar.

Alternativa scartata: persistere `target_effective` — richiede ricalcolo e re-save ad ogni variazione del maturato; il calcolo a runtime è più semplice e sempre aggiornato.

### 3. `deduct_break` su `accrual_configs`

Flag `BOOLEAN NOT NULL DEFAULT false`. Quando attivo, `query_metric()` raggruppa le entry per giorno e sottrae `user_profile.break_minutes` una volta per giorno prima di sommare i minuti. Identica logica al calcolo degli straordinari.

Il flag è a livello di `accrual_config` (non di widget) perché dipende dalla natura del tipo di voce: le ferie inserite con orario necessitano della detrazione, le ferie inserite come quantità giornaliera no.

## Conseguenze

- Nessuna breaking change — tutte le colonne hanno default sicuri.
- `WidgetComputed` aggiunge `target_effective: float | null` nella risposta API.
- Il frontend espone tre nuovi campi nell'editor accrual: carry-over, deduct-break, target %.
- Il template dell'admin widget editor (`user-widgets.component.html`) è una copia standalone (non condivide un componente con `widget-config-panel`) e ha ricevuto le stesse modifiche in parallelo.
