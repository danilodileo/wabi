# Wabi meeting
Learn how the dataset looks like so I can also explore it a bit- DONE

- 5k
- discard correlation

Check how many of the spectra have been assigned from those 5000 proteins


15 families have positive correlation between metaP and metaT
30k are unique proteins
The 5k proteins account from ~30-50% of abundance. so major proportion.

Do recA or ribosomial protein. do they behave differently as  metaT?
what about psbA. COG analysis or similar approach.

we are trying harmonizeR and also what Daniel suggested. To analize the data separately and apply Pearson correlation.

Using harmonizeR.
Either we use harmonizeR or we move to treat them separately.
let's make a decision now.

I would like to go for harmonizeR on 5k proteins.

# Notes for meeting 29 - 10 - 2025

## Metrics and tests
- Correlazione pairwise (per feature matched): Spearman rho (robusto su nonlineari) + p-value. Correggi p con Benjamini-Hochberg.
- Correlazioni multiple (tre vie): calcola coppie (G↔T, G↔P, T↔P). Mostra distribuzioni di rho e p.
- Distanze comunitarie: Bray-Curtis su abundance raw; Aitchison distance su CLR.
- Confronto strutturale sample-wise: Mantel tests (matrice distanza G vs T vs P). Permutazioni 999.
- Congruenza ordination: Procrustes tra PCA/PCoA di ciascun omico. Test significatività.
- Varianza spiegata: CCA / RDA per vedere quanto metaG predice metaT/metaP.
- Multi-omics integration: sparse PLS / DIABLO (mixOmics) o MOFA per trovare signatures comuni.
- Network: costruisci rete di co-occorrenza multi-omic (sparse correlations e threshold FDR). Visualizza community modules.

## Tax and funx analysis
Per ciascun livello (phylum, family, genome, COG_category):
Aggrega somme per sample.
Applica CLR.
Calcola correlazioni pairwise tra omici (Spearman + FDR).
Per ogni taxon/funzione calcola rho medio e percentuale di feature significativamente correlate.
Visualizzazioni: heatmap correlation matrix (taxa × omici), violin plot distribuzione rho per taxon, barplot % significant per taxon.
Per COG category: alluvial plot o stacked bar che mostra contributo relativo di G/T/P per categoria.

## Plots
- Scatter log–log gene-level: metaT vs metaP (punti = ORF). Colora per taxon; fit robust (loess). Mostra rho e n signif nel titolo.
- Distribuzione delle correlazioni: density / violin di Spearman rho per G↔T, G↔P, T↔P.
- Heatmap correlazioni tassonomiche: righe = taxa (phylum o family), colonne = coppie omiche, valori = median rho; annota significatività.
- Mantel matrix + heatmap: distanza(G,T,P) con p-values.
- Procrustes plot: mostra sovrapposizione ordination G vs T vs P e p-value.
- Per-genome summary: small multiples (12 pannelli o facets) con per-genome G/T/P stacked bars o line plots across samples.


## Hypothesis
- There is correlation at any level of omics for taxonomy?
    - which level show better correlation?
    - are the most abundant MAGs (MetaG or metaT) the one with higher correlation?
- There is correlation at any level of omics for functions?


## Possible plots
- Having a plot that shows the rate of abundance across different omics 
    (proteomics accounts 60% total abundance detectable, metaG 90% and metat 60% -how different each others?)
- Transcription efficiency plot (metaG vs metaT)
- Translation efficiency plot (metaT vs metaP)
- log-log plot (x-axis proteomics, y-metat) as done metaT and metaG - we can do it overall and then going from phylum -> genome
- log-log plot (x-axis proteomics, y-metat) as done metaT and metaG - we can do it overall and then going from phylum -> genome but for COG categories
- Correlation distribution: rho calculation for metaG↔metaT, metaG↔metaP, metaT↔metaP
- table summarising the correlation distribution at phylum level (it can be converted in heatmap)
| Phylum         | G↔T median rho (p) | G↔P median rho (p) | T↔P median rho (p) |
| -------------- | ------------------ | ------------------ | ------------------ |
| Actinobacteria | 0.32 (***)         | 0.07 (ns)          | 0.29 (**)          |
| Proteobacteria | 0.15 (*)           | −0.02 (ns)         | 0.21 (**)          |
| Bacteroidota   | 0.40 (***)         | 0.19 (*)           | 0.37 (***)         |


- Build PCA plots and perform Mantel test to check how different they are each others


fig9.3 visualisation of datasets for taxonomy - remove from May (maybe)

what about the third plot for metaP what is the y-axis?

not invent the wheel ourselves. there are papers that do similar analysis?

Write down how many protein, genes and transcripts we found

we started with 806 MAGs:
- how many we recruited with metaG/T/P?

- ask proportion of spectra we identified with iTRAQ and TMT.
- ask abundance of those 5k proteins.

Selecting 17 samples across all omics.
figure 9.4 only using top MAG from metaP


I would like to know, using those 17 samples:
There are phyla that shows more correlation than other? (proportion to the total)
in those phyla, there are families that show more correlation than other? (proportion to the phyla)
among those families, which MAGs are showing most correlation? (proportion to the family)

among those MAGs with correlation, are any COG categories that are more correlated than others?


having PCA for the three datasets then stackbar taxonomy
finally heatmap with all years together

4729 ORFs in proteomes

# Notes from meeting 3 - 11 - 2025
Functions: Overall patterns (stacked bar for all data)
(analogous analyses to 1, 2 & 3)
Which are dominant (COGs?)
What are the correlations per COG across MG, MT, MP
Focus the ORF level functional analyses on the dominant MAGs (i.e. MAGs with high correlations MG, MT, MP)
Visualize “dominants”:
COGs?
Most abundant individual proteins?
(two small sketches showing curves labeled “nt” and “np”, each with bars under the peaks)


# Notes for meeting 17 - 03 - 2026
## Project summary
### 15.1 Number of MAGs
We can give as supplementary all infos regarding MAGs or ORFs recruitment but we should focus only on those MAGs that have found in metaP after "harmonization". See Fig 15.2

### 15.2 Number of ORFs
Focus on 4724 ORFs, see Fig. 15.5

### 15.3 metaP dates subset
Very important. We should also focus on 17 samples. I know they are little, but I trust them more.

we can have several important questions among these 4724 ORFs.
- Do they describe the same community as metaG/T?
- Are them among the most abundand/transcribed ORFs?
- Do they come from relevant taxa?
- Which function they reppresent the most?

### 15.4 Representation of ORFs
Figure 15.9 gives an idea of ORFs identified among the 3 datasets in those 17 samples.

### 15.5 Ordination
The report describes several ordination plot (PCA). I will focus on few of my interest.

#### 15.5.1 ORFs
Figure 15.11 shows only the ORFs found in the subset, but I would have use only the 17 samples instead of all of them.

#### 15.5.2 MAGs
Figure 15.12 used the full set of MAGs and all samples for each omic dataset. I would instead:
- using only MAGs found in metaP; 
- OR using all MAGs to see how much the PCA of metaG/T shift away from metaP
- using only the 17 samples

#### 15.5.3 Family
both Figure 15.14 and Figure 15.15 are nice plot BUT it's hard to get something since he is using all samples instead of 17.

#### 15.5.4 Phylum
Same as for 15.5.3, nice figures but I would use only 17 samples

Figure 15.18. it's interesting because one can see that sometimes the most abundant genome in metaG is not well represented in metaT/P or vice versa. Sometimes this relationship is more coherent, hinting differences at species level.

### 15.6 Mantel tests
In general, mantel tests has a coefficient > 0.4 for all combinations which means that at least 40% of the PCA plots are coherent each others.

### 15.7 Heatmaps
Nice idea and it could be worth it to have them as supplementary but I would use only 17 samples and focus on visualising the differences!

## 17 - 03 - 2026

1. Among the 5k ORFs in metaP, how much they account for the % spectra. We have 40-60% of total proteome. but is the proteome the total spectra or only the 24,000 annotated ORFs?

2. How many these 5k ORFs account for % of metaT? (all reads mapped to the MAGs)
3. How many these 5k ORFs account for % of metaG? (all reads mapped to the MAGs)

4. make figure for taxonomy 1.2 - both 5k ORFs and total. e.g. top 10 fam in metaG, top 10 fam in metaT and top 10 fami in metaP.

- Do we have representative MAGs for these proteomic dataset?

- Do I have access to the dataset? I would like tables that I can open in RStudio.

/cfs/klemming/projects/supr/snic2020-16-76/projects/lmo_multiomics/NBIS-J_Pinhassi_2305