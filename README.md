# CRISPRa Positive Controls

This repository identifies candidate positive control genes for CRISPR activation (CRISPRa) viability screens. It analyzes published CRISPRa screening data to find genes that, when activated, consistently and strongly reduce cell viability across multiple cell lines — making them useful as positive controls in future CRISPRa screens.

The full analysis lives in [`CRISPRa_pos_ctrl_analysis.ipynb`](CRISPRa_pos_ctrl_analysis.ipynb).

## Data sources

| File | Description |
|---|---|
| `data/Sanson2018_Calabrese_reads_A375_MelJuSo.xlsx` | CRISPRa screen read counts (Calabrese library) in A375 and MelJuSo cell lines, from Sanson et al. 2018 |
| `data/Sanson2018_Calabrese_reads_A549_HCT116.csv` | CRISPRa screen read counts in A549 and HCT116 cell lines (unpublished) |
| `data/adhoc_sgRNA_disco_GRCh38_Ensembl_SpyoCas9A_strict.csv` | sgRNA annotation file (GRCh38, Ensembl, SpCas9), used to map sgRNAs to genes |
| `data/Sack2018_ORF_data.xlsx` | ORF overexpression screen data from Sack et al. 2018, used to validate candidates against known STOP genes |

## Analysis pipeline

The notebook performs the following steps, in order:

1. **Setup** — load read count, annotation, and validation data sets.
2. **Log normalization** — convert raw sgRNA read counts to log-normalized values.
3. **Filtering** — remove lowly represented sgRNAs based on plasmid DNA (pDNA) representation.
4. **Log-fold change (LFC) calculation** — compute per-sgRNA LFCs relative to the appropriate pDNA/no-drug reference for each condition.
5. **Combining samples** — merge replicate and condition-level data across cell lines.
6. **Annotation merge** — attach gene identifiers to sgRNA-level data.
7. **sgRNA-level z-scores** — z-score each sgRNA's LFC against the negative control distribution.
8. **Gene-level z-scores and statistics** — aggregate sgRNA z-scores to the gene level, and compute an empirical p-value/FDR for each gene using a negative-control (pseudogene) null distribution.
9. **Candidate identification** — select candidate positive control genes using:
   - a strongly negative median z-score across cell lines (large viability effect), and
   - a low median absolute deviation (MAD) of the z-score (consistency across cell lines/conditions).
10. **Validation** — cross-check candidates against STOP genes (genes with known strong negative effects on viability when overexpressed) from Sack et al. 2018.

## Outputs

| File | Description |
|---|---|
| `output/candidate_pos_ctrls.csv` | Final list of candidate positive control genes, with `median_z`, `mad_z`, `max_fdr`, and gene `symbol` |
| `output/plots/lfc_correlation_plot.png` | Correlation of sgRNA log-fold changes across replicates/conditions |
| `output/plots/volcano_plot.png` | Volcano plot of gene-level z-score vs. -log10(FDR) |
| `output/plots/candidate_plot.png` | Visualization of validated candidate genes against STOP gene status |

## Setup

Requires Python 3 with the packages listed in `requirements.txt`:

```bash
pip install -r requirements.txt
```

Then launch the notebook:

```bash
jupyter notebook CRISPRa_pos_ctrl_analysis.ipynb
```

Run all cells from the top; the notebook reads from `data/` and writes results to `output/`.
