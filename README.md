## Stage3_SARS-CoV-2_Analysis
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

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# ***CELL TYPES IDENTIFIED AT THE DIFFERENT STAGES OF SARS-CoV-2 INFECTION***
Ten distinct cell types were identified across all four datasets (***Tables 1a, 1b; Figures 1a, 2a, 3a, 4a, 6***). Five cell types were present at all timepoints: ciliated cells, airway goblet cells, ionocytes, alveolar macrophages, and pulmonary alveolar type I cells (AT1). Additional cell types were timepoint-specific: pulmonary alveolar type II cells (AT2) appeared only in Mock samples; airway epithelial cells were detected in Day 1 and Day 2 samples; Clara cells (club cells) were identified only in Day 2 samples; proliferated basal cell appeared in Day 3 samples. Basal cells emerged at Day 2 and Day 3, and at Day 3 a distinct proliferating basal cell population appeared.

***Table 1a.*** Cell type composition across SARS-CoV-2 infection time course.
<img width="660" height="433" alt="image" src="https://github.com/user-attachments/assets/aca26f5c-b106-46b1-8e02-24aba6d6b04e" />

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

<img width="1028" height="468" alt="image" src="https://github.com/user-attachments/assets/99842114-e35b-4b28-b86e-0211737be090" />

***Figure 5.*** PAGA graph visualization for Mock (negative control), Day 1, Day 2 and Day 3 post-infection samples. 

<img width="1028" height="437" alt="image" src="https://github.com/user-attachments/assets/65468eff-2c18-49ee-818b-6a5eb09f003b" />

***Figure 6.*** Cell annotated UMAP plot for merged (Mock, Day 1, Day 2 and Day 3) datasets (integrated analysis).

<img width="1028" height="530" alt="image" src="https://github.com/user-attachments/assets/6f18cfc2-342a-4d90-ae57-ee16a1784e7d" />

***Figure 7.*** Pseudotime analysis of merged (Mock, Day 1, Day 2 and Day 3) datasets (integrated analysis).

<img width="632" height="593" alt="image" src="https://github.com/user-attachments/assets/492cb161-d516-4325-a30d-627da3cb6ef2" />

***Figure 8.*** PAGA graph visualization merged (Mock, Day 1, Day 2 and Day 3) datasets (integrated analysis). 

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# ***HOW THESE CELL TYPES CORRELATE WITH COVID-19 INFECTION***
The identified cell types are typical for human bronchial epithelial cells (HBEC). The most common HBEC types found in my analysis were ciliated cells, goblet cells, Clara (club) cells, and basal cells. Other typical HBEC types like pulmonary neuroendocrine cells (PNECs) and tuft cells were not identified, likely due to their rarity (<1-2% of cells). (***Davis et al., 2021***)
***Ciliated cells as a primary target for viral infection***
Ciliated cells were the most abundant cell type across all timepoints according to both visual and quantification analyses (Mock: 6,233 cells; Day 1: 3,956; Day 2: 2,837; Day 3: 5,398). This corresponds to biological knowledge that ciliated cells dominate in the airway epithelium (***Davis et al., 2021***). These cells are also known to be the main target for viral infection in human airways (***Davis et al., 2021***). Cell annotated UMAP plots in the separate analysis demonstrate how the form of the ciliated cell cluster changes along the SARS-CoV-2 infection progression timeline. At Day 1 point it looks clefted without size changes, at Day 2 number of ciliated cells dramatically decreased and at Day 3 ciliated cell cluster recovered to the shape and size close to negative control samples. It happens together with augmentation of immunity cells (alveolar macrophages and airway epithelial cells) and basal cells in HBEC. This indicates that ciliated cells were damaged and then repaired during infection cycle. In all timepoints (Mock, Day 1, Day 2, Day 3) some trajectory heterogeneity of ciliated cells was observed; there is always a portion of ciliated cell cluster where cells have some higher pseudotime expression. However, integrated pseudotime analysis revealed even more sharp transcriptional heterogeneity in ciliated cells cluster, with Mock ciliated cells occupying lower pseudotime regions (healthy states) while ciliated cells from infected samples shifted to higher pseudotime values. This reflects infection-induced stress responses and altered gene expression. (***Figure 7***)
***Basal cells as a source of cell regeneration and repair processes during the viral infection***
Basal cells function as principal stem cells in the airway epithelium and can differentiate into club cells, PNECs, tuft cells, ionocytes, and other epithelial cells. Basal cells are highly important for airway epithelium regeneration. (***Davis et al., 2021***). This explains why basal cells increased during infection progression observed on cell annotated UMAP plots and in quantification analysis (Day 2: 447 cells; Day 3: 2,970 cells, plus 407 proliferating basal cells). The high demand for epithelium regeneration, especially to replace damaged ciliated cells, leads to basal cell activation and proliferation. Both separate and integrated pseudotime analyses positioned basal cells at mid-trajectory stages (***Figure 7***), which confirms that they are involved in late-stage regenerative response. Basal cells are localised and are not scattered along the pseudotime axis in integrated trajectory analysis, and this demonstrates that they appear progressively during infection rather than being constitutively present. There are at least two transcriptionally distinct basal cell subpopulations (Davis et al., 2021), which may explain why I found separate "basal cells" and "proliferating basal cells" clusters.
***Club cells as an additional cell regeneration source***
Clara (club) cells appeared and were only present at Day 2 (251 cells). Club cells are known as progenitors for ciliated and goblet cells (***Davis et al., 2021***) and their appearance at Day 2 corresponds to the maximum ciliated cell damage. This suggests club cells are recruited to help regenerate the damaged epithelium. Clara cells showed mid-high pseudotime values in integrated analysis visually (***Figure 7***) and quantitatively (0.43), which explains why day 2 samples had elevated mean pseudotime (0.47). This mid-high abundance of transcriptionally activated Clara (club) progenitor cells at Day 2 demonstrates this timepoint as critical transition point in SARS-CoV-2 infection process.
***Immune cells as an airway defence and homeostasis support***
Alveolar macrophages (AM) and lung epithelial cells (ECs) are responsible for immunity response, lung defence and airway homeostasis support (***Bissonnette et al., 2020***). AM were present at all timepoints (Mock: 3,411; Day 1: 39; Day 2: 337; Day 3: 787). They were present even in Mock samples because of their basic housekeeping functions. ECs appeared at Day 1 (79 cells) and Day 3 (2,569 cells), which may indicate immune response activation and higher need for defence during viral infection. It is noticeable that on Day 3 are 30-fold higher than at Day 1. 
AM have the highest pseudotime values for Mock, Day 1 and Day 3 in separate analysis and in the integrated analysis. As far as AM are biologically different form epithelial cells this one of possible explanations of their high pseudotime. However, another possible explanation is that they change their transcriptional state due to infection activation. ECs have more specific trajectory: they appear at Day 1 and then Day 3 in separate analysis and have higher pseudotime levels at Day 1. In integrated analysis ECs have trajectory heterogeneity (same as ciliated cells), which shows their changing transcriptional state within the viral infection process as part of the active immune response on the infection.
***Goblet cells and ionocytes as airway hydration and protection***
Goblet cells and ionocytes play role in airway protection: mucus production by goblet cells and ionocytes for ion transport for mucus hydration (***Davis et al., 2021***). 
In my study goblet cells (1,497-4,078 cells) remained present throughout infection. They have high pseudotime in Mock, mid-high pseudotime levels in Day 1 and Day 3 samples and low pseudotime at Day 2 in separate analysis; they also differ from expression activity of ciliated cells at all points. Ionocytes (916-3,993 cells) have the same trajectory patterns in Mock and along the infection development, their clusters are always spatially close to each other showing their functional interconnection. Both goblet cells and ionocytes had some cluster reduction on the Day 1 timepoint with further return to Mock cluster size. However, the integrated analysis revealed some difference in their trajectory behavior. Integrated analysis demonstrated that goblet cells remained present throughout infection with moderate transcriptional changes (intermediate pseudotime values). Ionocytes displayed noticeable transcriptional heterogeneity along the infection trajectory from healthy (low pseudotime) to activated (high pseudotime) states showing more dynamic functional adaptation to infection process than goblet cells. Thus, while goblet cells consistently produced mucus to trap pathogens, ionocytes had to adapt regulation of ion homeostasis for airways fluid balance, both serving for infection clearance.
***Unexpected findings***
Pulmonary alveolar type I (AT I) cells (Mock: 1,438; Day 1: 2,738; Day 2: 2,360; Day 3: 2,310) and type II (AT II) cells (2,672 cells in Mock) are not typical for bronchial epithelium but for alveoli in distal lungs (***Li et al., 2021***). Their identification could be explained either by sample collection when some alveolar cells were included, or by annotation challenges because some of their gene markers similar to epithelial cell types. While AT II cells were detected only in Mock samples, AT I cells showed transcriptional variations within individual infection stages in separate analysis; AT I cells showed minimal pseudotime variation and stable transcriptional profile in integrated analyses.
***Summary***
Overall, the cell type changes show how the airway responds to infection: ciliated cells get damaged, basal cells proliferate to repair the damage, club cells as progenitors help regeneration, immune cells produce immune response to the infection. Pseudotime trajectory analysis (***Figure 7***) supported with quantifications demonstrated the infection response progression: healthy mature epithelium (Mock, low pseudotime), then progenitor cell activation (Day 2) and active regeneration mediated by basal cell expansion (Day 3). 
Separate pseudotime analysis of individual infection stages revealed cellular heterogeneity at each timepoint. Day 2 showed particularly complex transcriptional patterns reflecting the simultaneous processes of damage, Clara cell activation, and early basal cell emergence. That characterizes Day 2 as the critical transition phase. (***Figures 1b, 2b, 3b, 4b***)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# ***ACE2 AS A POSSIBLE MARKER FOR TRACKING COVID-19 INFECTION RATE (BASED ON THIS DATASET)***
ACE2 (angiotensin-converting enzyme 2) is a receptor protein expressed on cell surfaces that serves as the primary entry point for SARS-CoV-2. (***Zhang et al., 2020***) While ACE2 is essential for viral entry, my analysis suggests it is not an ideal marker for tracking infection progression for several reasons. 
**Not cell type specific**
Though ACE2 is present in ciliated cells (the primary infection target) (***Zhang et al., 2020; Coden et al., 2021***), my analysis showed that its expression not limited by a single cell type or cluster.  ACE2 expression was detected across multiple cell types without strong enrichment in any specific population. This broad, low-level expression pattern reduces its utility as a specific COVID-19 infection rate marker.
**Lack of dynamic changes**
ACE2 expression remained relatively stable across infection timepoints. There were no dramatic increases or decreases of ACE2 expression corresponding to infection progression. Unlike other genes that show clear temporal patterns, ACE2 did not show specific expression changes throughout infection timepoints. Thus, ACE2 would not allow to monitor infection dynamics. 
Pseudotime trajectory analysis additionally confirmed limitations of ACE2 as COVID-19 tracking marker (***Figure 11b***). In the infection trajectory of cells from Mock (low pseudotime) to Day 3 (high pseudotime) states ACE2 displayed low uniform expression (dark violet coloring) across the entire trajectory. Thus, ACE2 expression failure to show a clear gradient or pattern along pseudotime which is required to effective infection progression marker.
**Low and scattered expression**
ACE2 showed very low expression levels across the dataset (mean expression: 0.04 overall; 0.04 in ciliated cells). Gene expression visualizations (***Figure 10a, b***) revealed sparse and scattered expression. Most cells showed either low or no detectable ACE2 expression. This low baseline expression also makes it difficult to use as a marker for COVID-19 infection tracking.
**Dependence on additional genes expression**
Ciliated cells are the primary target for viral infection because they express ACE2 and TMPRSS2 on their surface. (***Coden et al., 2021***) At the same time, successful viral infection requires not only ACE2 but also proteases (TMPRSS2, CTSL, TMPRSS4) to facilitate viral entry. Heatmap visualization of viral entry gene expression in ciliated cells (***Figure 9***) demonstrated the expression patterns of these genes across infection timepoints.
Visual and quantification analysis of these genes’ expression levels in the integrated dataset revealed much higher TMPRSS4 expression (1.72 overall, 1.20 in ciliated cells) compared to ACE2 (0.04). (***Figure 9***) So, ACE2 alone cannot reliably predict susceptibility to SARS-CoV-2 pathogen. Only combination of receptor and protease expression determines viral entry capacity. Therefore, tracking infection would require monitoring not ACE2 expression alone, but multiple viral entry genes expression.
**Trajectory inference analysis**
Cells ordering along infection progression trajectory during pseudotime analysis confirmed static pattern of ACE2 gene expression (***Figure 11b***). There was no visible clearly early-to-late gradient in the pseudotime coloring, which indicates low and relatively uniform ACE2 expression across all infection stages with no trajectory-dependent changes. Pseudotime analysis of individual infection stages (Mock, Day 1, Day 2, Day 3) revealed ACE2 expression was uniformly low and sparse within each timepoint with no dynamic changes across timepoints (***Figure 11a***). Thus, ACE2 cannot be COVID-19 infection marker because it does not show progressive changes along the biological timeline of infection.
**Conclusion**
ACE2 is functionally important for SARS-CoV-2 entry (***Zhang et al., 2020; Coden et al., 2021***). However, its low and sparse expression without important dynamics makes not fitting as a single marker for tracking COVID-19 infection rate or progression. Pseudotime analysis confirmed this: trajectory inference demonstrated that ACE2 gene expression remains static across the infection trajectory; there are no important progressive changes that would enable COVID-19 temporal tracking. To provide a more effective and accurate COVID-19 infection progression tracking it would be useful to combine ACE2 with protease expression (TMPRSS2, TMPRSS4, CTSL) or use viral RNA detection as direct evidence of infection.

<img width="541" height="668" alt="image" src="https://github.com/user-attachments/assets/c2e14bd2-0f22-44eb-b34f-1c4c3fe26c6e" />
<img width="270" height="162" alt="image" src="https://github.com/user-attachments/assets/307c0230-3a23-4e3b-a3c8-9922fc5d6097" />

***Figure 9.*** Heatmap of viral entry gene expression (ACE2, CTSL, TMPRSS2, TMPRSS4) in ciliated cells across infection stages (Mock, Day 1, Day 2, Day 3). 
Table shows mean expression levels in all cell types (overall) and ciliated cells specifically.

<img width="890" height="817" alt="image" src="https://github.com/user-attachments/assets/38896363-37b6-4a4a-8030-9f271f09364e" />

***Figure 10a.*** Cell annotated UMAP plots of ACE2 expression across infection stages (Mock, Day 1, Day 2, Day 3) from separate analyses. 

<img width="694" height="515" alt="image" src="https://github.com/user-attachments/assets/690abf98-280d-4e49-a457-7cebf6ece680" />

***Figure 10b.*** Cell annotated UMAP plot of ACE2 expression on integrated dataset combining all timepoints.

<img width="906" height="738" alt="image" src="https://github.com/user-attachments/assets/78cb3def-cd77-44df-848d-f67e7b6a4796" />

***Figure 11a.*** Pseudotime trajectory plots of ACE2 expression across infection stages (Mock, Day 1, Day 2, Day 3) from separate analyses. 

<img width="722" height="736" alt="image" src="https://github.com/user-attachments/assets/b6fdd2e2-e8be-4297-bd5a-85f79d799941" />

***Figure 11b.*** ACE2 expression on integrated pseudotime trajectory.


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# ***DIFFERENCE BETWEEN ENO2 AND ACE2 AS BIOMARKERS*** 
**Biological preface**
ENO2 (neuron-specific enolase) is a multifunctional protein mainly expressed in neurons and neuroendocrine cells. Its principal function is glycolysis in the cytoplasm, but it is also involved in several pathological processes including infection, inflammation, autoimmune diseases, and cancer (***Xu et al., 2019***). Unlike the original article, I did not identify distinct neuroendocrine cell clusters (likely due to their rarity and automated annotation). However, gene expression UMAPs revealed cells expressing ENO2 which confirms their neuroendocrine cells some presence (***Figure 12a, b***). These rare HBEC cells (<1-2%) can share gene expression patterns with other cell types and may not be detected by automated annotation methods.
ENO2 and ACE2 showed very different expression patterns during infection (***Figures 12, 13, 14***). ENO2 expression was more pronounced and showed clear dynamics through disease progression. It had cluster-specific patterns, especially in ciliated cells during the first two days, then expanded broadly across multiple cell types at Day 3 with highest expression still in ciliated cells.
**Visual observations**
ACE2 showed high but non-specific expression in Mock samples (negative control) and stayed low and sparsely expressed throughout all infection stages with no dynamic changes. This suggests ACE2 may be important for infection start but not for progression. On the contrary, ENO2 was more specifically expressed in Mock but then showed dramatic increase by Day 3. This means ENO2 may be partly involved in early infection but plays a more important role than ACE2 in infection development. This can be explained by ENO2's biological functions in metabolism and inflammation, which are crucial during SARS-CoV-2 infection. (***Figures 12a, 13***)
Pseudotime trajectory analysis confirmed this opposite expression behavior (***Figure 13, 14, 15***). ACE2 remained static across the infection trajectory and had uniformly low expression. ENO2 displayed temporal dynamics with marked elevation at Day 3. This ENO2 increase reflects gene expression response to infection, while ACE2 expression remained unchanged during infection process.
**Biological interpretation**
These expression patterns reflect the different biological roles of ACE2 and ENO2 proteins in SARS-CoV-2 infection. ACE2 serves as the viral entry receptor because its presence enables infection initiation, but its expression does not change in response to infection. ENO2's dramatic upregulation at Day 3 combined with its biological roles in cellular metabolism and inflammation (***Xu et al., 2019***), suggests that it is involved more in SARS-CoV-2 infection development and the inflammatory response. Thus, the expanded ENO2 expression across cell types at Day 3 (late infection stage) may reflect metabolic stress, inflammation, or neuroendocrine responses to viral infection.
**Conclusion**
According to my single-cell analysis with trajectory inference, ACE2 shows static expression pattern and low response to infection within the COVID-19 progression. This result matches with biological knowledge about ACE2 as a SARS-CoV-2 virus entry receptor and makes it a viral entry biomarker. On the other hand, ENO2 demonstrates dynamic expression pattern and active response to SARS-CoV-2 virus during COVID-19 progression. These observations correspond to ENO2’s biological functions in metabolism and inflammation and make it a more informative marker for tracking COVID-19 infection progression.

<img width="870" height="1591" alt="image" src="https://github.com/user-attachments/assets/6795f307-9c3b-430d-aa14-44c112da96e7" />

***Figure 12a.*** Cell annotated UMAP plots with ACE2 (left column) and ENO2 (right column) gene expression across infection stages (Mock, Day 1, Day 2, Day 3) from separate analyses. 

<img width="940" height="402" alt="image" src="https://github.com/user-attachments/assets/fb89eb55-750e-46e1-b425-11c4ee242227" />

***Figure 12b.*** Cell annotated UMAP plots with ACE2 (right panel) and ENO2 (left panel) gene expression on integrated dataset combining all timepoints.

<img width="940" height="318" alt="image" src="https://github.com/user-attachments/assets/abed2c74-06f1-48a6-95ee-559f0f0e1fbd" />

<img width="940" height="318" alt="image" src="https://github.com/user-attachments/assets/f3b26697-3981-49df-9923-3cb7f5ad3680" />

<img width="940" height="318" alt="image" src="https://github.com/user-attachments/assets/8611dad4-119c-4faa-abfd-6962ce406b85" />

<img width="940" height="318" alt="image" src="https://github.com/user-attachments/assets/27f73fee-8d0e-449d-9dbd-7381ac3eedf7" />

***Figure 13 a, b, c, d.*** ACE2 and ENO2 expression on separate pseudotime trajectories. Pseudotime orderings for (a) Mock, (b) Day 1, (c) Day 2, and (d) Day 3 showing ACE2 (left) and ENO2 (right) expression patterns within each infection stage.

<img width="919" height="312" alt="image" src="https://github.com/user-attachments/assets/ce14e082-3e89-424c-9abd-55c502b9d423" />

***Figure 14.*** ACE2 vs ENO2 expression on integrated pseudotime trajectory. Side-by-side comparison showing ACE2 (left) remains static while ENO2 (right) displays spatial variation along infection progression. 

<img width="525" height="534" alt="image" src="https://github.com/user-attachments/assets/b90be221-6b74-4a15-a693-10925e16d1e9" />

***Figure 15.*** ENO2 pseudotime expression. Visualization showing ENO2 dynamics along infection trajectory.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# ***CELL CLUSTER WITH THE HIGHEST ABUNDANCE OF ACE2 EXPRESSION AFTER DAY 3 TIMEPOINT AND BIOLOGICAL MEANING (VISUAL INTERPRETATION)***
**Visual observations**
The Day 3 UMAP plots reveals that ACE2 expression (brown dots on ACE2 gene expression UMAP) is most concentrated in the large purple cluster identified as ciliated cells (purple dots on cell annotated UMAP). ACE2-positive cells (brown dots) appear scattered across multiple cell types which are epithelial populations including basal cells and airway epithelial cells. However, the highest density and darkest coloring occur within the ciliated cell population. Other cell types show sparse, lighter ACE2 expression or lack expression entirely. (***Figure 16***)
**Biological interpretation**
Ciliated cells remain the primary target for SARS-CoV-2 throughout infection, and SARS-CoV-2 virus can still enter epithelial cells at Day 3 post-infection timepoint. Pseudotime analysis positioned Day 3 cells form mid to late trajectory stages (***Figure 17***), with ciliated cells distributed across both low pseudotime (close to healthy state expression) and mid pseudotime (infected cells expression) regions. The persistence of ciliated cells expressing ACE2 even at these advanced pseudotime stages indicates steady viral entry capability throughout the infection timeline.
Despite three days of infection and active basal cell regeneration (red and pink coloring clusters), a significant part of ciliated cell population still express ACE2. This suggests that epithelial regeneration has not yet fully replaced cells expressing ACE2.
The co-existence of ciliated cells fractions with ACE2 expression alongside proliferating basal cells (both visible at mid-to-late pseudotime) demonstrates simultaneous processes of active airway epithelial tissue regeneration and ongoing reinfection through the cells that are still susceptible to SARS-CoV-2 infection. 

<img width="1038" height="537" alt="image" src="https://github.com/user-attachments/assets/623afe81-8f18-407a-8b6a-52d4ba61c153" />

***Figure 16.*** ACE2 expression in Day 3 cell populations. *Left:* Cell type annotations on Day 3 UMAP. *Right:* ACE2 expression on Day 3 UMAP. ACE2 is most concentrated in ciliated cells (brown dots).

<img width="1028" height="367" alt="image" src="https://github.com/user-attachments/assets/cb752694-2556-4abc-998e-53764e1ab5fe" />

***Figure 17.*** ACE2 persistence in Day 3 ciliated cells along pseudotime. Pseudotime trajectory analysis showing ACE2 expression in ciliated populations (left panel), pseudotime ordering of Day 3 cells (central panel), and cell type distribution (right panel). 

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**REFERENCES**

*Bissonnette EY, Lauzon-Joset JF, Debley JS, Ziegler SF. Cross-Talk Between Alveolar Macrophages and Lung Epithelial Cells is Essential to Maintain Lung Homeostasis. Front Immunol. 2020 Oct 15;11:583042. doi: 10.3389/fimmu.2020.583042. PMID: 33178214; PMCID: PMC7593577.*

*Coden ME, Loffredo LF, Abdala-Valencia H, Berdnikovs S. Comparative Study of SARS-CoV-2, SARS-CoV-1, MERS-CoV, HCoV-229E and Influenza Host Gene Expression in Asthma: Importance of Sex, Disease Severity, and Epithelial Heterogeneity. Viruses. 2021 Jun 5;13(6):1081. doi: 10.3390/v13061081. PMID: 34198852; PMCID: PMC8226441.*

*Davis JD, Wypych TP. Cellular and functional heterogeneity of the airway epithelium. Mucosal Immunol. 2021 Sep;14(5):978-990. doi: 10.1038/s41385-020-00370-7. Epub 2021 Feb 19. Erratum in: Mucosal Immunol. 2022 Mar;15(3):528. doi: 10.1038/s41385-022-00500-3. PMID: 33608655; PMCID: PMC7893625.*

*Franzén, O., Gan, L. M., & Björkegren, J. L. (2019). PanglaoDB: a web server for exploration of mouse and human single-cell RNA sequencing data. Database, 2019*

*Li J, Tang N. Alveolar stem cells in lung development and regrowth. In: Nikolić MZ, Hogan BLM, eds. Lung Stem Cells in Development, Health and Disease (ERS Monograph). Sheffield, European Respiratory Society, 2021; pp. 17–30 [https://doi.org/10.1183/2312508X.10009520].*

*Rock, J. R., et al. (2009). Basal cells as stem cells of the mouse trachea and human airway epithelium. PNAS, 106(31), 12771-12775.*

*Xu CM, Luo YL, Li S, Li ZX, Jiang L, Zhang GX, Owusu L, Chen HL. Multifunctional neuron-specific enolase: its role in lung diseases. Biosci Rep. 2019 Nov 29;39(11):BSR20192732. doi: 10.1042/BSR20192732. PMID: 31642468; PMCID: PMC6859115.*

*Zhang H, Rostami MR, Leopold PL, Mezey JG, O'Beirne SL, Strulovici-Barel Y, Crystal RG. Expression of the SARS-CoV-2 ACE2 Receptor in the Human Airway Epithelium. Am J Respir Crit Care Med. 2020 Jul 15;202(2):219-229. doi: 10.1164/rccm.202003-0541OC. PMID: 32432483; PMCID: PMC7365377.*

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------





