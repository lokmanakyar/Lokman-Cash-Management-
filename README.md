Cash Management Model (Cost-Oriented, Proactive Closing)

Overview

This repository contains a cost-oriented cash management simulation and optimization for bank branches.
The model ingests daily branch transaction data (deposits/withdrawals, TL requests/transfers, ATM balances), simulates end-of-day cash decisions, and minimizes total cost (cost of cash + transport + counting). The simulation proactively targets the next day’s largest expected withdrawal to set today’s closing balance.

Key outcomes

Optimal cash threshold per branch-month (min total cost)

Daily simulated actions (top-ups, transfers), costs, and balances

Baseline vs. model cost comparison tables exported to Excel


Features

Proactive target closing: today’s target = max(optimal_threshold, next_day_max_withdrawal + safety_buffer)

Internal matching logic: a portion of deposits offsets withdrawals, capped by dynamic rules

Discrete transfer policy: first ≥ 100K TL, then +50K TL steps

Monthly per-branch optimization: Bayesian GP (skopt.gp_minimize) over a data-driven threshold range

Rich Excel outputs (formatted, multi-sheet summaries)


Data & Files

Input Excel: xa.xlsx (must be in the working directory)

Output Excel: saved to your Desktop as kasa_optimizasyon_sonuclari_proaktif.xlsx


Expected columns in xa.xlsx

TARIH, SUBE_KODU, HAFTA_NO, GUN_ADI, HAFTA_TIPI,
BASLANGIC_BAKIYE_TL, NAKIT_YATAN_TL, NAKIT_CEKEN_TL,
MAX_NAKIT_YATAN_TL, MAX_NAKIT_CEKEN_TL, ATM_DEN_GELEN_TL, ATM_YE_GIDEN_TL,
TL_TALEP, TL_DEVIR,
GUN_SONU_KASA_BAKIYE_TL_SUBE, ATM_AKSAM_KASA_TL,
ATM_NAKIT_CEKEN_MUSTERI_TL, ATM_NAKIT_YATAN_MUSTERI_TL,
GUNCELLENMIS_NAKIT_YATAN_TL, GUNCELLENMIS_NAKIT_CEKEN_TL

> Missing columns are auto-created as zeros; numeric fields are cleaned/coerced.



Method (Short)

1. Load & clean daily data; build baseline (real) costs from actual TL requests/transfers and cash-on-hand.


2. For each (branch, month):

Determine search bounds for the cash threshold (5th–95th pct of withdrawals with guards).

Optimize the threshold to minimize total cost via GP.

Simulate daily: apply internal matching, decide top-up or send-to-center under discrete rules, compute daily costs.



3. Export daily details and summaries (baseline vs. model).



Cost Model

Cost of Cash (daily): cash_end_of_day * (0.46 / 365)

Transport (per movement): ~472 TL

Counting (per 100K): ~130.6 TL

Discrete send policy: ≥100K, then +50K steps; never go below proactive target


> Constants can be tuned inside the script:



COST_OF_CASH_ORAN = 0.46 / 365
TASIMA_SABIT ≈ 472
SAYIM_100BIN ≈ 130.6
MIN_NAKIT_TRANSFER_ILK_ESIK = 100_000
NAKIT_TRANSFER_ARTIS_MIKTARI = 50_000
MIN_OPERASYONEL_KASA_ESIK = 50_000
... (internal matching thresholds & caps)

Requirements

Python 3.9+

Packages: pandas, numpy, scikit-optimize (skopt), xlsxwriter


Install:

pip install pandas numpy scikit-optimize xlsxwriter

How to Run

> The code is stored as a .txt for publishing. To execute, either rename to .py or copy into a Python file.



Option A — Rename and run

mv "Last Query Only Normal Cost Oriented.txt" cash_model.py
python cash_model.py

Option B — Copy content

Create cash_model.py, paste the script content, then:


python cash_model.py

Make sure xa.xlsx is in the same folder where you run the script.
On success, results are saved to:

~/Desktop/kasa_optimizasyon_sonuclari_proaktif.xlsx

Excel Outputs

1. Detaylı Günlük Sonuçlar
Per-day simulation: target closing, actions (top-up / transfer), daily costs, and model vs. real TL movements.


2. Özet Rapor (per branch-month)
Optimal threshold, model costs, baseline costs, risky-day count, average EoD cash.


3. Model Karsilastirma Ozeti
Concise baseline vs. model comparison with average balances and total costs.


4. Özet Rapor_2 (per branch, across months)
Aggregated totals (costs, risky days) and averages (thresholds, balances).



Troubleshooting

Excel file locked: Close the target file if already open and rerun.

Missing columns: Script will warn and fill zeros; verify your data mapping.

No (branch, month) groups: Check SUBE_KODU and valid TARIH parsing.


Notes & Safety

Use synthetic/anonymized data—never commit sensitive production data.

Parameterize costs to your institution’s official price list before decision-making.


License

Choose a license (e.g., MIT) if you plan to share publicly.
