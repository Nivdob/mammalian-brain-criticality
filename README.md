# mammalian-brain-criticality

Phylogenetic comparative analysis for:

> Ponce-Alvarez A, Sanz-Perl Y, Luppi AI, Vohryzek J, Natale E, Gini S, Gozzi A,
> Assaf Y, Yovel Y, Dobzinski N, van den Heuvel M, Kringelbach ML, Deco G.
> **The Critical Behavior of Brain Activity Across Mammalian Species.**
> *Advanced Science* (2026), e78873. https://doi.org/10.1002/advs.78873

This repository tests whether log brain volume predicts three model-derived
functional measures of brain dynamics across 104 mammal species, while
accounting for shared evolutionary history. It reproduces **Table 2** and
**Figure 5a** of the paper.

The spin and Hopf model simulations that produce the functional measures are in
[adrianponce/Spin-and-Hopf-models-MAMI](https://github.com/adrianponce/Spin-and-Hopf-models-MAMI).
The connectome data are available at https://doi.org/10.5281/zenodo.10372945.

## What the analysis does

1. Loads 100 time-calibrated trees sampled from the posterior distribution of
   the Upham et al. (2019) mammal phylogeny (via the `megatrees` package).
2. Matches the study species to the tree (9 names mapped to the closest species
   in the tree, 3 redundant species dropped) and removes 2 outliers
   (*Meriones unguiculatus*, *Dolichotis patagonum*), leaving 104 species.
3. Prunes each of the 100 trees to those species.
4. Fits PGLS regressions (`caper::pgls`, Pagel's λ by maximum likelihood) of
   heat capacity, susceptibility and metastability on log brain volume, on each
   tree, and summarizes them as medians with 2.5–97.5% quantile ranges.
5. Draws the 50% majority-rule consensus tree with median divergence times
   and the trait rings (Figure 5a).
6. Computes Blomberg's K (`phytools::phylosig`, 999 randomizations) for each
   trait on each tree.

## Repository contents

| Path | Description |
|------|-------------|
| `phylogenetic_analysis.Rmd` | The full analysis |
| `data/species_order_xyzw.txt` | Species names, one per line, in the row order of the trait files |
| `data/X_data.csv` | Log brain volume (one value per line, no header) |
| `data/Y_data.csv` | Critical heat capacity |
| `data/Z_data.csv` | Critical susceptibility |
| `data/W_data.csv` | Maximal metastability |
| `results_published/` | The exact result files reported in the paper (Table 2) |
| `output/` | Created when the analysis runs (not tracked) |

## How to run

Requirements: R (tested with R 4.4.1). The first chunk installs any missing
packages from CRAN (`ape`, `caper`, `phytools`, `megatrees`, `piggyback`,
`ggplot2`, `ggnewscale`, `dplyr`, `tidyr`) and Bioconductor (`ggtree`,
`ggtreeExtra`, `treeio`).

1. Clone this repository and open `phylogenetic_analysis.Rmd` in RStudio.
   The working directory must be the repository root.
2. Knit the file, or run all chunks.

On the first run, the 100 Upham trees (~30 MB) are downloaded and cached in
`data/Upham_100_full_mammal_trees.tre`. That file is not tracked by git.

All results are written to `output/`:

- `PGLS_data_curated_Upham.csv`: curated per-species data used in all analyses
- `Upham_100trees_pruned_curated_species.tre`: the 100 pruned trees
- `PGLS_results_100_Upham_trees.csv`, `PGLS_summary_100_Upham_trees_quantile_ranges.csv`
- `Blombergs_K_results_100_Upham_trees.csv`, `Blombergs_K_summary_100_Upham_trees.csv`
- `Upham_tree_tip_names.csv`: all tip names of the Upham tree (used for name matching)
- `Consensus_Upham_tree_paper.pdf` / `.png` (Figure 5a)

## Published results

`results_published/` holds the output files of the run reported in the paper
(Table 2): per-tree and summary results for PGLS and Blomberg's K.

Rerunning the analysis reproduces the PGLS results and the Blomberg's K
statistics exactly. The Blomberg's K **p-values** come from 999 random
permutations per tree, so they vary slightly between runs. Compare a rerun with
the files in `results_published/` for the reported values.

## Phylogeny reference

Upham NS, Esselstyn JA, Jetz W (2019). Inferring the mammal tree:
Species-level sets of phylogenies for questions in ecology, evolution, and
conservation. *PLoS Biology* 17(12): e3000494.
https://doi.org/10.1371/journal.pbio.3000494
