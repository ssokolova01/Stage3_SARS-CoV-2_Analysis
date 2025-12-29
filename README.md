### Stage3_SARS-CoV-2_Analysis
## SARS-Cov-2 Infection Dynamics: trajectory analysis on scRNA-seq data from SARS-CoV-2-infected human bronchial epithelial cells (HBEC).

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# COMPUTATIONAL ANALYSIS WORKFLOW
1. *Data Processing and Quality Control:* Single-cell RNA-seq data from four timepoints of SARS-CoV-2 infection process (Mock, Day 1, Day 2, Day 3) were filtered by parameters: cells with <10% mitochondrial RNA, genes per cell > 200, genes expressed in >3 cells. Doublets were removed using Scrublet.
2. *Normalization and Feature Selection:* Data were normalized and log transformed. Top 2,500 of highly variable genes was selected for further analysis.
***Separate Dataset (Timepoint) Analysis:*** each dataset (timepoint) was analyzed independently. 
3. *PCA and UMAP dimensionality reduction* were performed. 
4. *Leiden clustering* (resolution 0.5) cell populations identification in each infected state. 
5. *Cell type annotation* using PanglaoDB database (parameters: markers = ‘Lungs’, tmin=2). Further manual annotation for NA clusters (Day_2 and Day_3 timepoints) was performed based on marker gene expression.
5. *Pseudotime analysis* was performed separately for each infection timepoint dataset to examine cellular heterogeneity within infection stages.
***Integrated Dataset (Timepoint) Analysis:*** After quality control, normalization and feature selection individual datasets were merged for infection progression analysis.
3. *Batch correction* was applied using BBKNN (Batch-Balanced K-Nearest Neighbors) method which suits the best for data variations correction along with preserving biological difference.
4. *UMAP and Leiden clustering* were performed on the integrated and batch-corrected combined dataset.
5. *Cell annotation* was conducted using the same parameters as for the separated timepoint analysis.
6. *Trajectory Analysis:* during integrated pseudotime analysis Mock ciliated cell were selected as a as trajectory root to order cells along infection progression from healthy to infected states. PAGA (Partition-based Graph Abstraction) was computed to visualize relationships between cell type clusters. Trajectory visualization was performed with Force-directed graph layout (ForceAtlas2).
***Gene Expression Analysis:*** For both separate and integrated datasets gene expression of viral entry genes (ACE2, TMPRSS2, TMPRSS4, CTSL) and SARS-CoV-2 infection biomarkers (ENO2) was quantitatively and visually analysed across cell types, timepoints, and pseudotime trajectories using UMAP plots, violin plots, and heatmaps.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# INTRODUCTION
***Introductory explanations.***

This analysis was performed using a publicly available single-cell RNA-sequencing dataset from Ravindra et al. (2021): "Single-cell longitudinal analysis of SARS-CoV-2 infection in human airway epithelium identifies target cells, alterations in gene expression, and cell state changes."
Datasets were analysed using two approaches: separate analysis of individual SARS-CoV-2 infection timepoints and integrated analysis of combined datasets. Quality control (QC), normalization and cell annotation parameters were same for both analyses: filtering thresholds (mitochondrial content <10%, minimum 200 genes per cell, minimum 3 cells per gene) and doublet removal using Scrublet during QC; 2,500 highly variable genes were selected for feature selection; lung-specific marker genes from the PanglaoDB database with a minimum threshold of 2 marker genes per cell type (tmin=2) for cell type annotation.
For the integrated analysis, individual datasets were first processed separately through quality control, normalization, and feature selection. Then, datasets were merged, batch correction was applied using BBKNN (Batch-Balanced k-Nearest Neighbors) to construct a batch-aware neighbor graph to reduce batch effect and keep biological distinction between infection timepoints.
For technical reasons and to have biologically meaningful output, UMAP dimensionality reduction and Leiden clustering were performed on the batch-corrected neighbor graph (BBKNN method), but during trajectory inference a standard neighbor graph was applied for PAGA and pseudotime analysis.

Cell annotation required additional steps. Most cell types were successfully assigned through automated annotation using PanglaoDB lung markers. However, some clusters remained unassigned (NA) and required manual annotation. In separate dataset analysis two timepoint datasets had NA clusters after automated annotation, Day 2 and Day 3. One cluster for Day 2 timepoint (cluster 2) was manually annotated as basal cells (expression of canonical basal cell markers (KRT5, KRT14), adhesion genes (COL17A1, DST) and progenitor markers (IGFBP6, S100A2)). For two clusters in Day 3 timepoint (clusters 7 and 8), cluster 7 was manually annotated as basal cells, expressing keratins (KRT5, KRT14, KRT15), basement membrane proteins (ITGB1, COL17A1) and epithelial stem cell markers (S100A2); cluster 8 was manually annotated as proliferating basal cells, expressing cell cycle and proliferation markers (MKI67, TOP2A, CENPF), mitotic genes (TUBA1B, STMN1), and chromatin organization factors (HMGB1, HMGB2, H2AFZ), which are typically expressed in actively dividing cells. These manual annotation cell types align with the biological context of SARS-CoV-2 infection where epithelial damage triggers basal cell proliferation for tissue regeneration. In combined dataset analysis one cluster (cluster 9) remained unannotated after automated assignment. Manual annotation found expression of basal cell markers (KRT5, KRT14, KRT15), basement membrane proteins (COL17A1, DST, ITGB1), progenitor markers (S100A2, IGFBP6), and stress response genes (SFN, CAV1). This expression profile defines cluster 9 as basal cell type and is consistent with the basal cell populations identified in separate timepoint analyses. (***Rock et al., 2009***)
To perform manual annotation, differentially expressed genes for each NA cluster were examined using Wilcoxon rank-sum test and matched to known airway epithelial cell types based on established markers in the literature. Cell type annotation was performed using ‘Lungs’ marker genes from PanglaoDB database (***Franzén et al., 2019***).

***Contradictory findings.*** Comparison analysis of viral entry gene expression in ciliated cells revealed some inconsistencies with the original study. Though ACE2 (mean expression: 0.04 overall, 0.04 in ciliated cells) and CTSL (0.20 overall, 0.42 in ciliated cells) expression rates were compatible with Ravindra et al. (2021), TMPRSS2 and TMPRSS4 genes expression pattern differed to the published results. While the original study reports higher TMPRSS2 expression, my analysis revealed that TMPRSS4 expression (1.72 overall, 1.20 in ciliated cells) was approximately 6-fold higher than TMPRSS2 (0.29 overall, 0.36 in ciliated cells). This inconsistency was first noted during gene expression visual analysis by heatmap. This finding may reflect differences in normalization and batch correction parameters, computational pipeline implementation or some biological variations across samples in analyzed datasets. (***Figures 9, 18, 19***)

<img width="834" height="683" alt="image" src="https://github.com/user-attachments/assets/4c728aaf-2d84-408c-9f18-67b12881d98c" />
***Figure 18.*** ACE2, ENO2, CTSL, TMPRSS2, TMPRSS4 gene expression on separate pseudotime trajectories.

<img width="882" height="721" alt="image" src="https://github.com/user-attachments/assets/3a0f6532-a4fb-4db3-9cb0-bd7c78383262" />
***Figure 19.*** Cell annotated UMAP plot of ACE2, CTSL, TMPRSS2, TMPRSS4 gene expression on integrated dataset combining all timepoints.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
***QUESTION 1.***
Ten distinct cell types were identified across all four datasets (***Tables 1a, 1b; Figures 1a, 2a, 3a, 4a, 6***). Five cell types were present at all timepoints: ciliated cells, airway goblet cells, ionocytes, alveolar macrophages, and pulmonary alveolar type I cells (AT1). Additional cell types were timepoint-specific: pulmonary alveolar type II cells (AT2) appeared only in Mock samples; airway epithelial cells were detected in Day 1 and Day 2 samples; Clara cells (club cells) were identified only in Day 2 samples; proliferated basal cell appeared in Day 3 samples. Basal cells emerged at Day 2 and Day 3, and at Day 3 a distinct proliferating basal cell population appeared.

***Table 1a.*** Cell type composition across SARS-CoV-2 infection time course.
<img width="658" height="435" alt="image" src="https://github.com/user-attachments/assets/533e04f7-fa2b-4d25-8e8d-4c313f05cdc5" />

***Table 1b.*** Cell type composition across SARS-CoV-2 infection time course (alternative version).
<img width="660" height="274" alt="image" src="https://github.com/user-attachments/assets/ce9aa98e-457d-4622-b358-a0629f6eddfe" />

Pseudotime trajectory analysis was applied to both individual datasets (shows cellular heterogeneity within each stage) and integrated dataset (shows infection progression) (***Figures 1b, 2b, 3b, 4b, 5, 7, 8***). Pseudotime analysis of separate datasets revealed cellular heterogeneity at each infection stage, but this heterogeneity was more pronounced in Mock and Day 2 timepoints. At Day 2 ciliated cell transcriptional profiles appeared more similar to Mock patterns compared to Day 1 and Day 3 in terms of pseudotime distance; in both timepoints (Mock, Day 2) they had a high gene expression difference with other cell types. This potentially reflects a transient critical infection phase before the extensive cellular changes observed at Day 3. (***Figures 1b, 2b, 3b, 4b, 5***) Pseudotime analysis of combined dataset showed the temporal emergence of basal cells, which localized around middle trajectory stages (***Figures 7, 8***); this corresponds to their appearance as a regenerative response during infection. 

Two rare cell types reported in the original study, pulmonary neuroendocrine cells (PNECs) and tuft cells, were not identified in my analysis. These results likely related to annotation approaches and detection sensitivity. In respect that these cell types represent <1-2% of airway epithelial cells, it makes them challenging to detect with automated annotation methods. Additionally, ionocytes and tuft cells share overlapping gene expression patterns, because they both derive from secretory progenitors and are both involved in airway immunity. This potentially lead to their merged classification in my analysis. (***Davis JD, 2021***)

<img width="940" height="409" alt="image" src="https://github.com/user-attachments/assets/2449866d-2d16-4b15-a1ae-ee5fbf1e6fcd" />
***Figure 1a.*** Cell annotated UMAP plots for Mock samples (negative control). 

<img width="940" height="396" alt="image" src="https://github.com/user-attachments/assets/d8cebbd0-d5a8-4552-a9a1-c5d21eb0a02c" />
***Figure 1b.*** Pseudotime analysis of Mock samples displayed on graph layout.

<img width="1034" height="451" alt="image" src="https://github.com/user-attachments/assets/36ebc413-66df-46fd-89f2-7a4d39ce9fa6" />
***Figure 2a.*** Cell annotated UMAP plots of Day 1 post-infection samples. 

<img width="1034" height="436" alt="image" src="https://github.com/user-attachments/assets/3cf4f4d9-f6e4-4817-83c9-7ba396a9adac" />
***Figure 2b.*** Pseudotime analysis of Day 1 post-infection samples displayed on graph layout.

<img width="1046" height="455" alt="image" src="https://github.com/user-attachments/assets/dab4256b-7228-4133-a8c3-0b346ea74af3" />
***Figure 3a.*** Cell annotated UMAP plots of Day 2 post-infection samples. 

<img width="1034" height="436" alt="image" src="https://github.com/user-attachments/assets/f561da8c-80ce-4f9c-bce8-9a51cf2d909a" />
***Figure 3b.*** Pseudotime analysis of Day 2 post-infection samples displayed on graph layout.

<img width="1034" height="449" alt="image" src="https://github.com/user-attachments/assets/fb202e28-bc18-40a3-902b-2a6bcc2704d0" />
***Figure 4a.*** Cell annotated UMAP plots of Day 3 post-infection samples. 

<img width="1034" height="436" alt="image" src="https://github.com/user-attachments/assets/8d020366-7b2d-440a-840c-28f060f51ee2" />
***Figure 4b.*** Pseudotime analysis of Day 3 post-infection samples displayed on graph layout.



