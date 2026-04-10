# 1. Repository Overview
- Purpose: Stablecoin depegging early-warning study using PCA-based signals across major stablecoins (UST, USDC, USDT, USDP/PAX, DAI) with coverage 2020-11-25 to 2026-03-19.
- Components: raw data, cleaning/feature-engineering notebooks, multiple PCA experiment tracks (rolling/expanding, pooled, out-of-sample), and stage 2 evaluation outputs (splits, metrics, plots). Repo is notebook-driven with CSV/Parquet intermediates; few standalone scripts.

# 2. Top-Level Directory Map
- `raw_data/` – original daily stablecoin price histories plus macro sentiment/rate files (CSV).
- `clean_data/` – cleaned per-coin Parquet outputs (`*_clean.parquet`, `*_final.parquet`) used by downstream notebooks.
- `phase_1/` – early exploratory pipeline: cleaning notebooks, PCA drafts, and intermediate clean CSVs in `phase_1/clean_data/` plus legacy raw data in `phase_1/raw_data/`.
- `data_cleaning.ipynb`, `feature_engineering.ipynb` – main current preprocessing notebooks (top-level) producing cleaned/final Parquet files.
- `pca_expandingwindow/`, `pca_rollingwindow_30/60/90/` – PCA experiment folders with notebooks and matched depeg loadings, z-score band plots, and hit summaries.
- `stage1_pca/` – consolidated stage-1 PCA outputs (expanding + rolling variants) with summary CSVs and band plots; includes driver notebooks `PCA_code_expanding_new.ipynb` and `PCA_code_rolling_new.ipynb`.
- `hybrid_pooled_pca.ipynb`, `pooled_pca.ipynb`, `pooled_pca_alt_ver.ipynb` – pooled/combined PCA experiment notebooks.
- `oos_pca.ipynb`, `oos_pca_revised.ipynb`, `oos_pca_ust_factor.ipynb` – out-of-sample PCA testing notebooks.
- `stage2_pca_results/` – train/test PC matrices and loadings for three split schemes (globaldate, customdate, equaldepeg).
- `stage2_split_*.csv`, `stage2_final_summary.csv` – aggregated performance metrics per coin and split rule.
- `stage2_plots/` & `plots/` – publication-style figures (PC plots, signal timelines, peg deviation figures) and generated images from notebooks.
- `analysis/` – small collection of summary notebooks/tables (e.g., `stage1_summary.ipynb`).
- `pca_results/` – present but empty (reserved for future outputs).
- `summary_statistics_visualizations.ipynb` – new report-ready descriptive notebook (do not alter existing data).
- `README.md` – minimal placeholder.

# 3. Data Inventory
- Raw data: `raw_data/*.csv` (per-coin historical prices, fear & greed index, fed funds rate, etc.); legacy raw files in `phase_1/raw_data/` for earlier experiments.
- Cleaned data: `clean_data/*_clean.parquet` and final engineered `clean_data/*_final.parquet` (per coin; include symbol, timestamp, prices, market cap/volume, depeg labels, peg errors). Phase-1 equivalents stored as CSV in `phase_1/clean_data/` (e.g., `cleaned_data_for_pca.csv`).
- Engineered features: embedded in the `*_final.parquet` files (fields like `peg_error`, `abs_peg_error`, rolling deviations); additional feature engineering steps shown in `feature_engineering.ipynb`.
- Split datasets / PCA matrices: `stage2_pca_results/<scheme>/<coin>_{pc_train,pc_test,loadings,explained_variance}.csv`.
- Matched depeg loadings & hit summaries: in `pca_expandingwindow/` and `pca_rollingwindow_*` folders (`matched_depeg_loadings_*.csv`, `summary_hit_days_all_*.csv`, `detailed_hit_days_all.csv`).
- Stage 1 summaries: `stage1_pca/rolling_*/expanding/*summary_*` CSVs plus band PNGs.
- Final evaluation tables: `stage2_final_summary.csv`, `stage2_split_*_{summary,sensitivity,pc_breakdown}.csv`.
- Figures: `stage2_plots/*.png` (PC visuals per coin), `plots/stage2_split_*_signal.png`, `plots/figure1_price_behaviour_may2022.png`, `plots/figure2_boxplot_abs_peg_error.png`.

# 4. Code and Notebook Inventory
- Preprocessing: `data_cleaning.ipynb` (ingest raw CSVs, clean per coin), `feature_engineering.ipynb` (add peg error metrics, event labels, saves `_final.parquet`). Phase-1 cleaning notebooks (`phase_1/cleaned_*`) mirror earlier steps for specific sources.
- Exploratory comparisons: `phase_1/stablecoin_comparison.ipynb`, `summary_statistics_visualizations.ipynb` (new descriptive plots & stats).
- Stage-1 PCA drivers: `stage1_pca/PCA_code_expanding_new.ipynb` and `PCA_code_rolling_new.ipynb` likely run expanding and rolling PCA over cleaned datasets and emit summary CSVs/plots under `stage1_pca/expanding` and `stage1_pca/rolling_*`.
- Rolling/Expanding experiment notebooks: `pca_expandingwindow/PCA_expandingwindow.ipynb`, `pca_rollingwindow_30/60/90/PCA_code_rolling*.ipynb` generate matched loadings, hit days, and band plots.
- Pooled/hybrid PCA: `pooled_pca.ipynb`, `pooled_pca_alt_ver.ipynb`, `hybrid_pooled_pca.ipynb` combine coins into pooled PCA frameworks; likely read `_final.parquet` and write plots/metrics (outputs mostly in `plots/` or local CSVs inside folders).
- Out-of-sample analysis: `oos_pca.ipynb`, `oos_pca_revised.ipynb`, `oos_pca_ust_factor.ipynb` test signal transferability and factor models; outputs feed `plots/` and stage2 tables.
- Stage-2 aggregation: notebooks not explicitly listed, but CSVs (`stage2_split_*`, `stage2_final_summary`) suggest compiled metrics from the above PCs and signals.
- Misc/analysis: `analysis/stage1_summary.ipynb` provides summary views; `.ipynb_checkpoints/` hold autosaves.

# 5. Pipeline Reconstruction (likely order)
1. **Raw ingestion** – Place/update source CSVs in `raw_data/` (or legacy `phase_1/raw_data/`).
2. **Cleaning** – Run `data_cleaning.ipynb` to produce per-coin `*_clean.parquet` in `clean_data/` (phase-1 cleaning notebooks were earlier prototypes).
3. **Feature engineering & labeling** – Run `feature_engineering.ipynb` to add peg error metrics and depeg labels, writing `*_final.parquet` to `clean_data/`.
4. **Stage-1 PCA (expanding/rolling)** – Execute `stage1_pca/PCA_code_expanding_new.ipynb` and `PCA_code_rolling_new.ipynb` (or legacy `pca_expandingwindow` & `pca_rollingwindow_*` notebooks) to compute PCs, z-score bands, and hit summaries.
5. **Pooled/Hybrid PCA** – Optional pooled analyses via `pooled_pca*.ipynb` and `hybrid_pooled_pca.ipynb` to compare cross-coin factors.
6. **Out-of-sample / Stage-2 splits** – Use `oos_pca*.ipynb` (and related utilities) to train/test on predefined splits, producing PC matrices and loadings in `stage2_pca_results/` and performance tables `stage2_split_*` plus figures in `plots/` and `stage2_plots/`.
7. **Final aggregation & reporting** – `stage2_final_summary.csv` consolidates signal metrics; figures saved in `plots/` and `stage2_plots/`; `summary_statistics_visualizations.ipynb` provides descriptive Section 2.4 visuals.

# 6. Reproducibility Notes
- Run sequence: `data_cleaning.ipynb` → `feature_engineering.ipynb` → stage1 PCA notebooks (`stage1_pca/*.ipynb` or `pca_rollingwindow_*`/`pca_expandingwindow`) → pooled/hybrid (optional) → out-of-sample `oos_pca*.ipynb` → compile stage2 summary tables/plots.
- Dependencies: Python 3 with pandas, numpy, matplotlib; seaborn optional; pyarrow/fastparquet likely required for Parquet I/O. No requirements file provided; environment setup must be done manually.
- File paths are relative and consistent; ensure `raw_data/` is populated before cleaning. Some notebooks may reference specific column names (e.g., `close`, `symbol`, `timestamp`) and expect `_final.parquet` outputs.
- Outputs are written to existing directories (`clean_data`, `stage1_pca/*`, `pca_rollingwindow_*`, `stage2_pca_results`, `stage2_plots`, `plots`). Avoid overwriting unless rerunning intentionally.
- Potential pain points: absent `nbformat` package in environment for programmatic runs; minimal README guidance; notebooks may need sequential execution to populate intermediate CSVs before later steps; check hardcoded date windows (e.g., Terra crash visuals) when extending coverage.

# 7. Suggested Report-Ready Summary
The project repository is organized around a notebook-driven workflow that traces stablecoin prices from raw ingestion through feature engineering and a suite of PCA-based depegging diagnostics. Raw daily price and macro sentiment data reside in `raw_data/`, with legacy exploratory sources archived under `phase_1/raw_data/`. Cleaning and harmonization are handled in the top-level notebooks `data_cleaning.ipynb` and `feature_engineering.ipynb`, which standardize timestamps, symbols, and peg-error measures before writing per-coin Parquet files (`*_final.parquet`) to `clean_data/`.

Stage 1 analysis develops baseline factors using both expanding and rolling PCA windows. Driver notebooks in `stage1_pca/` and the parallel `pca_expandingwindow` and `pca_rollingwindow_30/60/90` directories compute principal components, z-score bands, and depegging hit summaries, exporting CSV diagnostics and PNG band plots. Complementary pooled and hybrid PCA experiments (`pooled_pca*.ipynb`, `hybrid_pooled_pca.ipynb`) test cross-coin factor structures using the same cleaned inputs.

Out-of-sample and split-based evaluations form Stage 2. The `oos_pca*.ipynb` notebooks generate train/test PC matrices and loadings for three split schemes (global date, custom date, equal depeg), with results stored in `stage2_pca_results/`. Aggregated performance tables (`stage2_split_*` and `stage2_final_summary.csv`) and per-coin PC visualizations (`stage2_plots/` and `plots/`) document signal quality and lead times. Newly added `summary_statistics_visualizations.ipynb` supplies descriptive figures and summary statistics that motivate the empirical framework.

Reproduction follows a linear sequence: populate `raw_data/`, run cleaning and feature engineering notebooks to refresh `clean_data/`, execute Stage 1 PCA notebooks to create intermediate diagnostics, then run out-of-sample notebooks to populate Stage 2 result folders and plots. Because the repository lacks an environment file, users should provision Python with pandas, numpy, matplotlib (plus seaborn/pyarrow) and respect the expected column names (`timestamp`, `symbol`, `close`, peg-error fields). Output paths are predefined; reruns will overwrite existing CSVs/PNGs unless redirected.

Limitations stem from minimal README guidance, notebook-only orchestration, and potential hardcoded windows or column assumptions. Nonetheless, the data and intermediate outputs are laid out transparently, allowing a motivated user to trace each stage from cleaned Parquet inputs through PCA factors to final evaluation tables and figures.

# 8. Appendix-Friendly File Map
| Path | Type | Purpose | Key Inputs / Outputs |
| --- | --- | --- | --- |
| raw_data/*.csv | Raw data | Daily prices + macro sentiment/rates | Source CSVs → consumed by cleaning notebooks |
| clean_data/*_clean.parquet | Clean data | Per-coin cleaned datasets | from raw; input to feature engineering |
| clean_data/*_final.parquet | Engineered data | Peg-error & labels added | output of feature_engineering; input to PCA notebooks |
| data_cleaning.ipynb | Notebook | Cleaning pipeline | reads raw_data → writes *_clean.parquet |
| feature_engineering.ipynb | Notebook | Feature creation & labeling | reads *_clean.parquet → writes *_final.parquet |
| stage1_pca/PCA_code_expanding_new.ipynb | Notebook | Expanding-window PCA | reads *_final.parquet → `stage1_pca/expanding/*` CSV/PNG |
| stage1_pca/PCA_code_rolling_new.ipynb | Notebook | Rolling PCA (30/60/90) | reads *_final.parquet → `stage1_pca/rolling_*/*` CSV/PNG |
| pca_expandingwindow/PCA_expandingwindow.ipynb | Notebook | Legacy expanding PCA | reads final data → matched loadings, band plots |
| pca_rollingwindow_30/60/90/PCA_code_rolling*.ipynb | Notebooks | Legacy rolling PCA variants | reads final data → matched loadings, hit summaries |
| pooled_pca*.ipynb, hybrid_pooled_pca.ipynb | Notebooks | Pooled/combined PCA experiments | reads final data → plots/metrics (plots/) |
| oos_pca*.ipynb | Notebooks | Out-of-sample & split evaluation | reads final data → `stage2_pca_results/`, `plots/`, `stage2_plots/` |
| stage2_pca_results/<scheme>/*.csv | Results | Train/test PCs, loadings, explained variance | from oos notebooks |
| stage2_split_*_{summary,sensitivity,pc_breakdown}.csv | Results | Split-level performance tables | aggregated metrics |
| stage2_final_summary.csv | Results | Consolidated signal metrics | aggregation of Stage 2 outputs |
| stage2_plots/*.png | Figures | PC visualizations per coin | from stage2 processing |
| plots/stage2_split_*_signal.png | Figures | Signal timelines | from oos/split notebooks |
| plots/figure1_price_behaviour_may2022.png | Figure | Terra crash price plot | from descriptive notebook |
| summary_statistics_visualizations.ipynb | Notebook | Descriptive stats & two report figures | reads final data → inline/plots outputs |
| analysis/stage1_summary.ipynb | Notebook | Summary diagnostics | reads stage1 outputs |
| pca_expandingwindow/ & pca_rollingwindow_* matched_depeg_loadings_*.csv | Intermediate | Depeg loading matches per coin | from legacy PCA notebooks |
