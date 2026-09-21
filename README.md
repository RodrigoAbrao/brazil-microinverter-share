# Brazil — microinverter share of on-grid PV systems ≤ 10 kW

Monthly share of **microinverter** systems among grid-connected rooftop PV systems up to 10 kW in Brazil, built from the public ANEEL distributed-generation registry, plus the system-level data behind it, a brand ranking and a three-year scenario projection.

Base date of the ANEEL snapshot: **2026-08-31** (downloaded 2026-09-20). Last **settled** month: **April 2026**. May–August 2026 are still being backfilled by ANEEL and are flagged as provisional (see below).

Companion page with charts and method: `pitch_micro_br_china.html` (open it in a browser).

## Headline (settled months only)

| | Jan–Mar 2025 avg | Feb–Apr 2026 avg | Change |
|---|---|---|---|
| Micro share by number of systems | 12.7% | 16.2% | +3.5 pp (+28%) |
| Micro share by kW | 11.6% | 14.9% | +3.3 pp |
| Micro systems per month | 9,373 | 12,129 | +29% |
| All on-grid ≤10 kW systems per month | 73,758 | 74,657 | +1% |

Scenarios for July 2029 (share by count): conservative 17% · base 28% · accelerated 37%, from a 15.9% anchor (mean of Nov 2025–Apr 2026). Ceilings and the accelerated pace are declared premises, not fits.

## Files

| File | What it is |
|---|---|
| `systems/systems_le10kw_<year>.csv.gz` (2018 … 2026) | **One row per system** (0 < kW ≤ 10) connected in that year, with the inverter classification, one file per year from 2018 to Aug 2026 (about 3.8 million rows in total). Gzip-compressed CSV, UTF-8. |
| `monthly_micro_share_le10kw.csv` | Monthly aggregate since 2018: counts and kW by inverter type, shares, 3-month smoothing, provisional flag. |
| `micro_brands_le10kw_2025-01_2026-04.csv` | Microinverter brand ranking (systems and kW) for Jan 2025–Apr 2026. |
| `forecast_monthly_to_2029-07.csv`, `forecast_checkpoints.csv` | Scenario projection, monthly and at July checkpoints. |
| `chart_measured.png`, `chart_forecast.png` | The two charts. |

### Columns of `systems/systems_le10kw_*.csv.gz`

| Column | Meaning |
|---|---|
| `cod_aneel` | ANEEL system id (`CodEmpreendimento`) |
| `uf`, `municipio` | State and municipality |
| `classe_consumo` | Consumer class as registered (Residencial, Comercial, Rural…) |
| `data_conexao`, `mes` | Grid-connection date (`DatConexao`) and its month |
| `potencia_kw` | System power in kW as registered |
| `fabricante_registrado`, `modelo_registrado` | Inverter manufacturer and model exactly as typed in the registry (free text) |
| `marca_efetiva` | Normalised inverter brand; proprietary microinverter model codes are reattributed to their maker (e.g. `HMS-…` → Hoymiles, `SUN2000G3` → Deye) even when the registry names the distributor |
| `tipo_inversor` | Detailed class: `micro`, `string`, `hybrid`, `unknown` (model not recognised), `module` (a PV module was typed into the inverter field) |
| `classificacao` | **`micro`** (microinverter) · **`normal`** (string inverter, incl. unknown/module) · **`hybrid`** (battery inverter, excluded from the on-grid denominator) |
| `mes_provisorio` | `True` for months still being backfilled by ANEEL at the base date (May–Aug 2026) |

No personal data is included (owner name, tax id and coordinates were dropped).

### Key columns of `monthly_micro_share_le10kw.csv`

`n_micro`, `n_string`, `n_hybrid`, `n_unknown`, `n_module`, `n_total` (and `kw_*`) · `n_ongrid = n_total − n_hybrid` · **`share_n_pct = 100·n_micro/n_ongrid`** (headline, by count) · `share_kw_pct` · `share_n_lt10_incl_hyb_pct` (the public-dashboard definition: < 10 kW strictly, hybrids in the denominator) · `ratio_vs_trailing3` (count vs. the previous 3 months, backfill diagnostic) · `provisional`.

## Definitions and caveats

- **Band**: 0 < system power ≤ 10 kW, inclusive. **On-grid** = every system in the band except hybrid (battery) inverters (0.33% of systems).
- **Classification** is rule-based on the free-text brand and model fields, applied identically to every year. Systems mixing a string inverter and microinverters on one record count as micro. About 6% of systems in 2025–26 have an unrecognised model and are counted as `normal`; that share is higher in 2018–2020, when more records carry a blank or garbled manufacturer, so early-year micro shares are a floor rather than an exact value (see `n_unknown` in the monthly file).
- **Provisional months.** ANEEL keeps adding connections to a month for 3–4 months after it ends. A month is flagged provisional while its count is below 90% of the previous three months' mean. Provisional months **read high**: the states that report early (MG, RJ, SP) are the high-micro states; the ones still missing run at 4–12%. Restricted to the same states, the unfinished months sit only ~1 pp above their settled level. **Do not quote May–Aug 2026 shares.** July 2026 is ~34% filled and reads 22.3%; when completed it should land near 17%.
- **Projection**: logistic curve toward a declared ceiling (20 / 38 / 55%), initial slope matched to the measured OLS slope (post-step drift, whole-window pace, 1.5× whole-window pace), anchored on the mean of the last six settled months. Measured = solid, projected = scenario assumption.

## Source and reproduction

Source: ANEEL open data — *Empreendimentos de Geração Distribuída* (cadastro + informações técnicas fotovoltaicas), all 27 states. Data are as registered by distribution utilities and are not audited.

Generated by `examples/micro_share_le10.py`, `micro_share_le10_forecast.py` and `export_micro_share_le10_systems.py` of the `inversores-market-share` project. Own classification and analysis.
