# Wabi multi-omics workflow

**Project:** Wabi — integrated metaG, metaT, metaP analysis of marine microbial communities  
**Last updated:** 2026-04-02  
**Status:** in progress

---

## Overview

Two parallel narratives developed throughout all analyses:

- **Narrative A** — descriptive: what the integrated dataset reveals about microbial community function at genome/family/KO level
- **Narrative B** — methodological critique: limits of environmental metaP for ecosystem-level inference, and recommendations for the field

---

## Background and key numbers

| Parameter | Value |
|---|---|
| Total ORFs in dataset | ~1M |
| MetaP ambiguous proteins | ~20k |
| MetaP matched ORFs (after filtering) | ~5k |
| MetaP matched ORFs with metaT/metaG | 4,724 |
| Families in metaP | — |
| Genomes (MAGs) in metaP | 17 |
| Mean ORFs per MAG (metaP) | 19 |
| Max ORFs in single MAG (metaP) | 172 |
| MAGs with only 1 ORF (metaP) | 6 |
| Genomes in metaT | 28 |
| Mean ORFs per MAG (metaT) | ~1,240 |
| Time points (samples) | 10 |
| ORFs with significant Spearman correlation (metaT/metaP) | 27 (FDR-corrected) |

**Key findings already established:**

- MetaP-matched ORFs capture ~3–5% of total abundance in both metaT and metaG. Random sets of equal size capture only ~0.2–0.3% (bootstrap n=1000) → metaP ORFs are biased toward **high-abundance** genes (~10–15x more abundant than random). Consistent with MS detection bias toward abundant proteins.
- Two non-mutually exclusive explanations: (1) functional bias toward constitutive/stable proteins; (2) taxonomic bias toward dominant taxa. To be resolved by KO analysis (Step 5).
- Correlation analysis (CLR-transformed, Spearman + Pearson where normal): 27 ORFs significant after FDR correction (Spearman); 803 with Pearson. Final metric to be decided before Step 5.

---

## Step 1 — MetaP coverage assessment

**Narrative:** B  
**Responsible:** Danilo  
**Status:** partially done (single random draw); needs bootstrap

**Rationale:**  
Establish whether the metaP-detected ORFs represent a random or biased sample of the metatranscriptome. The result underpins all subsequent methodological caveats.

**Input:**
- Master table (long format: orf × sample, with TPM, RPKM, proportion)
- List of metaP-matched ORFs (~5k after ambiguous removal)

**Analysis:**
1. Bootstrap (n=1000): for each iteration draw a random set of ORFs equal in size to matched ORFs from the full metaT ORF pool
2. For each iteration and each sample: compute % TPM explained by the random set
3. Compare distribution of % TPM (bootstrap) against observed % TPM for matched ORFs

**Expected output:**
- Boxplot: % TPM coverage per sample — matched ORFs vs bootstrap distribution (with 95% CI)
- Table: per-sample observed coverage vs bootstrap mean and CI

**Key statement (Narrative B):**  
"MetaP-detected ORFs are significantly depleted in metaT TPM relative to a random set of equal size, indicating that proteomics detection is biased toward low-transcript-abundance genes and does not represent a random sample of the transcriptionally active community."

---

## Step 2 — Functional profile of metaP subset vs metaT

**Narrative:** A + B  
**Responsible:** Danilo  
**Status:** to do

**Rationale:**  
Characterize which KO functions are detected by metaP and whether they reflect the dominant functional signal in the metatranscriptome.

**Input:**
- Master table
- KO annotations (orf_annotations.tsv.gz)

**Analysis:**
1. Compute KO-level relative abundance in metaP subset (proportion) and in full metaT (TPM)
2. Identify over/under-represented KO categories in metaP relative to metaT (hypergeometric test or Fisher's exact)
3. Optionally aggregate to KEGG pathway level

**Expected output:**
- Paired barplot or heatmap: KO category distributions metaP vs metaT
- Table: enriched/depleted KO categories in metaP

**Key statements:**
- Narrative A: "the detected proteome is functionally dominated by X"
- Narrative B: "metaP functional profile over-represents Y relative to the metatranscriptomic signal, consistent with detection bias toward stable/abundant proteins"

---

## Step 3 — Family-level cross-omic concordance

**Narrative:** A  
**Responsible:** collaborator  
**Status:** to do

**Rationale:**  
Test whether the most abundant families in metaT/metaG are also the most abundant in metaP. Already known to show good concordance — this step formalizes and quantifies it.

**Input:**
- Master table aggregated to family level

**Analysis:**
1. Spearman correlation between family-level relative abundance in metaT vs metaP, and metaG vs metaP, across the 10 samples
2. Rank families by abundance in each omic layer and compare rankings

**Expected output:**
- Scatter plot: family abundance metaT vs metaP (and metaG vs metaP)
- Correlation coefficient per sample and overall

**Key result already known:**  
Good correlation at family level — the most abundant families in metaT/metaG are also the most abundant in metaP.

**Key statement (Narrative A):**  
"At the family level, community composition inferred from metaP is consistent with metaT and metaG, suggesting that dominant taxonomic groups are robustly detected by proteomics despite low overall ORF coverage."

---

## Step 4 — Genome-level cross-omic concordance

**Narrative:** A + B  
**Responsible:** collaborator  
**Status:** to do

**Rationale:**  
Test whether family-level concordance holds at genome (MAG) level. Identify which MAGs drive concordance or discordance, and whether metaP detection is skewed toward specific genomes within a family.

**Input:**
- Master table aggregated to genome level
- Spearman correlations already computed at ORF level

**Analysis:**
1. For each MAG: n ORFs detected in metaP, n with significant metaT/metaP Spearman correlation (FDR-corrected), % significant
2. Are the most abundant MAGs in metaT/metaG the same as those most detected in metaP across the 10 samples?
3. Is there a skewed distribution of metaP-detected ORFs across MAGs within specific taxa (e.g. one MAG dominating within a family)?

**Expected output:**
- Scatter plot: MAG abundance in metaT vs metaP ORF detection, colored by family
- Table: per-MAG summary (n_orfs_metaP, n_correlated, pct_correlated, family, mean abundance metaT/metaG)
- Distribution plot: ORF counts per MAG within each family

**Key statements:**
- Narrative A: "concordance at family level is driven by specific dominant MAGs within each family"
- Narrative B: "even in the best-represented MAG (172 ORFs, ~14% of metaT ORFs for that genome), only ~1% of ORFs show significant metaT/metaP correlation — family-level concordance masks strong genome-level heterogeneity"

---

## Step 5 — KO-level correlation analysis

**Narrative:** A  
**Responsible:** collaborator  
**Status:** to do

**Rationale:**  
Identify which specific functions show consistent cross-omic signal across samples, and whether they form interpretable biological patterns (e.g. ribosomal, housekeeping, stress-responsive, pathway-specific).

**Input:**
- Master table
- KO annotations
- ORF-level Spearman correlations

**Analysis:**
1. Identify top 100 KO terms by correlation strength (metaT vs metaP), FDR-corrected
2. Characterize functional pattern: are correlated KOs enriched in ribosomal proteins, constitutive housekeeping genes, stress-responsive functions, or specific KEGG pathways?
3. Are correlated KOs distributed across multiple taxa or concentrated in specific families/MAGs?

**Expected output:**
- Table: top 100 correlated KOs with functional annotation and KEGG pathway assignment
- Enrichment test: correlated KOs vs full metaP subset (hypergeometric)
- Heatmap or dot plot: correlated KOs × family/MAG (taxonomic distribution)

**Key statements:**
- Narrative A: "functions with strongest metaT/metaP concordance are enriched in X — suggesting these represent the most translationally stable processes in the community"
- Narrative B: if dominated by ribosomal/structural proteins → "consistent with methodological bias toward stable, abundant proteins rather than ecologically responsive functions"

---

## Step 6 — Synthesis and methodological recommendations

**Narrative:** B  
**Responsible:** both  
**Status:** pending results from Steps 3–5

**Rationale:**  
Explicitly delimit what can and cannot be inferred from environmental metaP data at current coverage levels. Provide a reference framework for the field.

**Input:** outputs from all steps

**Analysis:**
1. Summary table: per family — metaP ORF coverage, % correlation, dominant KOs, MAG-level heterogeneity
2. Qualitative comparison with isolate-based literature (*Rhodobacter sphaeroides*, *Trichodesmium*) as baseline for expected metaT/metaP concordance
3. Note: no published benchmark exists for % ORFs with significant metaT/metaP correlation at single-ORF level — this itself is a gap in the field

**Expected output:**
- Synthesis table
- Discussion text

**Key statements:**
- "Family-level abundance patterns are interpretable from metaP data, but mask genome-level heterogeneity"
- "ORF-level cross-omic concordance is low even in well-represented MAGs, consistent with isolate-based literature showing broad metaT/metaP discordance"
- "Current environmental metaP yield is insufficient for ecosystem-level functional inference"
- "No published benchmark exists for ORF-level metaT/metaP correlation rate in environmental samples — establishing this baseline is a contribution of the present study"
- Methodological recommendations: deeper proteome sampling, improved extraction for complex marine communities, standardization across studies

---

## Analysis priority and responsibility

| Priority | Step | Responsible | Status |
|---|---|---|---|
| 1 | Step 1 — bootstrap coverage | Danilo | in progress |
| 2 | Step 3 — family concordance | collaborator | to do |
| 3 | Step 4 — genome concordance | collaborator | to do |
| 4 | Step 5 — KO correlation | collaborator | to do |
| 5 | Step 2 — functional profile | Danilo | to do |
| 6 | Step 6 — synthesis | both | pending |

---

## Notes and open questions

- Pearson vs Spearman: decide final metric before Step 5
- ~20k ambiguous metaP proteins excluded — relevant for Narrative B, consider supplementary note
- KEGG pathway aggregation deferred to second pass after KO-level analysis

---

## Observations (2026-04-02)

### Taxonomic concordance hierarchy

Correlation analysis performed at three levels, all based on CLR-transformed abundances across 10 samples. Results aggregated bottom-up: ORF → genome → family → phylum.

**Phylum level** (families significant per phylum, Spearman p_adjust < 0.05):
63 families significant, distributed across 11 bacterial phyla. Pseudomonadota dominates in absolute numbers (28/53, 53%) but Bacteroidota has highest proportion (10/16, 63%). No phylum significantly overrepresented after FDR correction. Bdellovibrionota and Thermoproteota: 0 significant families.

**Family level** (concordant genomes per family):
Focusing on families with ≥3 genomes: Ilumatobacteraceae (11/15, 73%), Nanopelagicaceae (14/20, 70%), Crocinitomicaceae (7/10, 70%) show highest proportion. Rhodobacteraceae (8/17, 47%) and Pelagibacteraceae (3/7, 43%) relatively low despite ecological prominence. Nitrosopumilaceae (0/2): no concordant genomes.

**ORF level** (significant ORFs per genome, aggregated by family):
Among well-represented families (n_orf_total ≥ 50): Flavobacteriaceae highest (5.3%), followed by Burkholderiaceae (3.4%) and Ilumatobacteraceae (3.0%). Nanopelagicaceae (2.1%), Rhodobacteraceae (2.1%), Pelagibacteraceae (1.1%), Cyanobiaceae (1.5%) all very low. One D2472 genome shows negative correlation (-0.763).

**Overall pattern:** concordance is distributed across families with no strong overrepresentation at any level. Percentages remain low even in the best-represented families, consistent with the broad metaT/metaP decoupling observed at ORF level.


