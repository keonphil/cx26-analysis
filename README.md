# Cx26 fluorescent CO₂ sensor: reanalysis in Python

Rebuilding the data analysis from my BSc research project in Python. The original analysis was done by hand in Excel, ImageJ and GraphPad Prism. The aim is to create a reproducible pipeline and check how well the reported results hold up when rebuilt from the underlying spreadsheets.

## Background

The project tested whether GFP or circularly permuted GFP (cpGFP) inserted into extracellular loop 2 of connexin 26 (Cx26) could report CO₂-driven changes in the channel. Constructs were expressed in HeLa cells and imaged live at different PCO₂. Per-cell fluorescence changes were calculated in Excel, covering:

- fluorescence change in GFP and cpGFP constructs from 35 mmHg (control) to 55 mmHg/depolarisation
- dose-response (35, 55 and 70 mmHg)
- K125R mutants of both constructs
- a longer-linker (LL) GFP variant

## Pipeline

- `01_tidy.ipynb`: loads the per-cell results from 7 Excel sheets into one tidy table (one row per cell per condition)
- `02_stats.ipynb`: reproduces the statistics: Mann-Whitney U (WT vs K125R, WT vs longer linker) and Friedman with Dunn's post-hoc (dose-response)
- `03_plots.ipynb`: box plots with individual points

Libraries: pandas, scipy, matplotlib, openpyxl.

## Reproducing the original results

Medians, U statistics and p-values were checked against the values in my dissertation. Most reproduced exactly. The differences are in the validation log below.

## Data validation log

Rebuilding the analysis in code exposed three errors in my original analysis:

1. **Blanks converted to zeros.** Some cells came from a batch where I didn't apply the depolarising solution, so their depolarisation values were blank. The Excel formula converting fractions to percentages turned those blanks into 0, and the zeros were then included in the depolarisation medians, pulling them towards zero. Fixed by dropping blanks instead of treating them as 0. The overall conclusion doesn't change, but the original depolarisation medians were wrong.
2. **Duplicate value.** One K125R value appears twice with identical values to many decimal places, so it is probably one cell counted twice. Removing it moves the K125R result from right on p = 0.05 to just above it.
3. **Sample size mismatch.** The cpGFP dose-response sheet has 17 cells, but the dissertation reports n = 16. The Friedman statistic differs slightly as a result (the conclusions are unchanged), and I can't now identify which cell was excluded or why.

## Limitations

- The pipeline starts from the per-cell results tables. The windows used to calculate baseline and response from raw fluorescence traces were picked by hand in Excel, so that step isn't automated here.
- Small sample sizes and a single fluorescence channel (no ratiometric reference), as covered in the dissertation.
- The WT vs K125R result for GFP is borderline (p = 0.0497 in Prism, 0.0502 in scipy), so it shouldn't be read as a clear effect.

## Data

The data is not included in this repository, as it comes from unpublished lab work. `data/` is git-ignored. To run the notebooks you need the original spreadsheets in `data/`: `Delta.xlsx` and `GFP_K125_2.xlsx`.

## Running it

```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Then run the notebooks in order.

## AI use

I used Claude to help plan, edit, and debug this project. I typed, ran, and debugged the code, then checked the outputs against my dissertation. Claude also flagged data issues in the validation log, and I confirmed each one in the original spreadsheets.

## Author

Keon Phillips