## Normalization

The first step in the DE analysis workflow is count normalization, which is necessary to make accurate comparisons of gene expression between samples.

<img src="../img/deseq_workflow_normalization.png" width="200">

The counts of mapped reads for each gene is proportional to the expression of RNA ("interesting") in addition to many other factors ("uninteresting"). Normalization is the process of scaling raw count values to account for the "uninteresting" factors. In this way the expression levels are more comparable between and/or within samples.

The main factors often considered during normalization are:
 
 - **Sequencing depth:** Accounting for sequencing depth is necessary for comparison of gene expression between samples. In the example below, each gene appears to have doubled in expression in sample A, however this is a consequence of sample A having double the sequencing depth. 
 
 >***NOTE:** In the figures below, each pink and green rectangle represents a read aligned to a gene. Reads connected by dashed lines connect a read spanning an intron.*
 
    <img src="../img/normalization_methods_depth.png" width="400">
 
 - **Gene length:** Accounting for gene length is necessary for comparing expression between different genes within the same sample. The number of reads mapped to a longer gene can appear to have equal count/expression as a shorter gene that is more highly expressed. 
 
    <img src="../img/normalization_methods_length.png" width="200">
 
 - **RNA composition:** A few highly differentially expressed genes can skew some types of normalization methods. Accounting for RNA composition is recommended for comparison of expression between samples, and is particularly important when performing differential expression analyses [[1](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-10-r106)]
    
    <img src="../img/normalization_methods_composition.png" width="400">
    
While normalization is essential for differential expression analyses, it is also necessary for exploratory data analysis, visualization of data, and whenever you are exploring or comparing counts between or within samples.
 
### Common normalization methods

Several common normalization methods exist to account for these differences:

- **CPM (counts per million):** counts scaled by total number of reads. This method accounts for sequencing depth only.
- **TPM (transcripts per kilobase million):** counts per length of transcript (kb) per million reads mapped. This method accounts for both sequencing depth and gene length.
- **RPKM/FPKM (reads/fragments per kilobase of exon per million reads/fragments mapped):** similar to TPM, as this method also accounts for both sequencing depth and gene length as well; however, it is **not recommended**.
- **Tool-specific metrics for normalization:** 
	- DESeq2 uses a median of ratios method, which accounts for sequencing depth and RNA composition [[1](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-10-r106)]. 
	- EdgeR uses a trimmed mean of M values (TMM) method that accounts for sequencing depth, RNA composition, and gene length [[2](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-3-r25)]
	
 ### RPKM/FPKM (not recommended)
 
While TPM and RPKM/FPKM normalization methods both account for sequencing depth and gene length, RPKM/FPKM are not recommended. **The reason  is that the normalized count values output by the RPKM/FPKM method are not comparable between samples.** 

Using RPKM/FPKM normalization, the total number of RPKM/FPKM normalized counts for each sample will be different. Therefore, you cannot compare the normalized counts for each gene equally between samples. 

**RPKM-normalized counts table**

| gene | sampleA | sampleB |
| ----- |:-----:|:-----:|
| XCR1 | 5.5 | 5.5 |
| WASHC1 | 73.4 | 21.8 |
| ... | ... | ... |
|Total RPKM-normalized counts | 1,000,000 | 1,500,000 |

For example, in the table above, SampleA has a greater proportion of counts associated with XCR1 (5.5/1,000,000) than does sampleB (5.5/1,500,000) even though the RPKM count values are the same. Therefore, we cannot directly compare the counts for XCR1 (or any other gene) between sampleA and sampleB because the total number of normalized counts are different between samples. 

### TPM (recommended for within sample comparisons) 

In contrast to RPKM/FPKM, TPM-normalized counts normalize for both sequencing depth and gene length, but have the same total TPM-normalized counts per sample. Therefore, the normalized count values are comparable both between and within samples.

> *NOTE:* [This video by StatQuest](http://www.rna-seqblog.com/rpkm-fpkm-and-tpm-clearly-explained/) shows in more detail why TPM should be used in place of RPKM/FPKM if needing to normalize for sequencing depth and gene length.

### Tool-specific metrics (recommended for between sample comparisons)

Tool-specific metrics of normalization are often the best methods for comparing counts between samples. These methods account for the composition of the sample, so that the normalization factors are not skewed by outlier or differentially expressed genes.

>**NOTE:** EdgeR's **TMM method** of normalization is recommended for **all comparisons** (within and between samples).

# Quality Control

The next step in the DESeq2 workflow is QC, which includes sample-level and gene-level steps to perform QC checks on the count data to help us ensure that the samples/replicates look good. 

<img src="../img/deseq_workflow_qc.png" width="200">

## Sample-level QC

A useful initial step in an RNA-seq analysis is often to assess overall similarity between samples: 

- Which samples are similar to each other, which are different? 
- Does this fit to the expectation from the experiment’s design? 
- What are the major sources of variation in the dataset?

Log2-transformed normalized counts are used to assess similarity between samples using Principal Component Analysis (PCA) and hierarchical clustering. DESeq2 uses a **regularized log transform** (rlog) of the normalized counts for sample-level QC as it moderates the variance across the mean, thereby improving the distances/clustering for these visualization methods.

<img src="../img/rlog_transformation.png" width="500">

Sample-level QC allows us to see how well our replicates cluster together, as well as, observe whether our experimental condition represents the major source of variation in the data. Performing sample-level QC can also identify any sample outliers, which may need to be explored to determine whether they need to be removed prior to DE analysis. 

<img src="../img/sample_qc.png" width="700">

### [Principal Component Analysis (PCA)](https://hbctraining.github.io/DGE_workshop/lessons/principal_component_analysis.html)

Principal Component Analysis (PCA) is a technique used to **emphasize variation** and bring out strong patterns in a dataset. To explore the variation in the data, we could **plot the expression values of each gene for each of our *n* samples against each other** in *n*-dimensional spaces. In the figure below we can see this if we had only two samples, but once we have more than 3 samples, we cannot visualize the samples in *n*-dimensional space.

<img src="../img/PCA_2sample_genes.png" width="600">

PCA provides a dimensionality reduction technique that finds the greatest amounts of variation in a dataset and assigns it to principal components.

The principal component (PC) explaining the greatest amount of variation in the dataset is PC1, while the PC explaining the second greatest amount of variation in the data is PC2, and so on and so forth until PC*n*.

PCA plots two PCs against each other, generally focusing on PC1 and PC2, which explain the largest amounts of variation in the data. **If two samples have similar levels of expression for the genes that contribute significantly to the variation represented by PC1, they will be plotted close together on the PC1 axis.** Therefore, we would expect that biological replicates to have similar scores (since the same genes are changing in the same directions) and cluster together on PC1 and/or PC2, and the samples from different treatment groups to have different score. **This is easiest to understand by visualizing example PCA plots.**

In the PCA plot below we can see our samplegroups separate on PC1, this is generally what we hope for. PC1 explains 89% of the variation in the data, so it's likely that this variation is due to differences between our samplegroups, EN (pink) and ENR (blue).

<img src="../img/PCA_example4.png" width="400">

We can use other variables **present in our metadata** to explore other causes of the variation in our data:

<img src="../img/PCA_example5.png" width="400">

We can determine that the 5% of variation in the data represented by PC2 is due to variation between individuals in this paired design example.

In the next example, the metadata for the experiment are displayed below. The main condition of interest is `treatment`.

<img src="../img/example_metadata.png" width="400">

When visualizing on PC1 and PC2, we don't see the samples separate by `treatment`. To explore the sources of variation in the data, we explore the other factors.

The `cage` factor does not seem to explain the variation on PC1 or PC2.

<img src="../img/example_PCA_cage.png" width="400">

While the `sex` factor appears to separate samples on PC2.

<img src="../img/example_PCA_sex.png" width="400">

The `strain` factor explains the variation on PC1.

<img src="../img/example_PCA_strain.png" width="400">

Since we can identify the likely sources of variation driving PC1 and PC2, we can account for that variation in the model and regress it out. We continue to look for variation in our data due to treatment.

<img src="../img/example_PCA_treatmentPC3.png" width="400">

In the final example, we can visualize the samples clustering by genotype on PC2 (13% variance). **If we saw one of the red samples below clustering with the blue samples (or vice versa), we might be worried about a mix-up. It would give us sufficient cause to remove that sample as an outlier and/or do some follow-up tests in the lab.**

<img src="../img/PCA_example1.png" width="400">

We can see that the plasmid expression level represents the major source of variation in the data on PC1 (55% variance).

<img src="../img/PCA_example2.png" width="400">

PCA is also a nice way to look for batch effects. In the below figure, we see batch 1 separate distinctly from batches 2 and 3.

<img src="../img/PCA_example6.png" width="400">

Even if your samples do not separate by PC1 or PC2, you may still get biologically relevant results from the DE analysis, just don't be surprised if you do not get a large number of DE genes. To give more power to the tool for detecting DE genes, it is best to account for  major, known sources of variation in your model. 

For details regarding the calculations performed for PCA, we encourage you to explore [StatQuest's video](https://www.youtube.com/watch?v=_UVHneBUBW0). 

***
**Exercise**

The figure below was generated from a time course experiment with sample groups 'Ctrl' and 'Sci' and the following timepoints: 0h, 2h, 8h, and 16h. 

- Determine the sources explaining the variation represented by PC1 and PC2.
- Do the sample groups separate well?
- Do the replicates cluster together for each sample group?
- Are there any outliers in the data?
- Should we have any other concerns regarding the samples in the dataset?

<img src="../img/PCA_example3.png" width="600">

***

### Hierarchical Clustering Heatmap

Similar to PCA, hierarchical clustering is another, complementary method for identifying strong patterns in a dataset and potential outliers. The heatmap displays **the correlation of gene expression for all pairwise combinations of samples** in the dataset. Since the majority of genes are not differentially expressed, samples generally have high correlations with each other (values higher than 0.80). Samples below 0.80 may indicate an outlier in your data and/or sample contamination.  

The hierarchical tree can indicate which samples are more similar to each other based on the normalized gene expression values. The color blocks indicate substructure in the data, and you would expect to see your replicates cluster together as a block for each sample group. Additionally, we expect to see samples clustered similar to the groupings observed in a PCA plot. 

In the plot below, we would be a bit concerned about 'Wt_3' and 'KO_3' samples not clustering with the other replicates. We would want to explore the PCA to see if we see the same clustering of samples.

<img src="../img/heatmap_example.png" width="500">

***
**Exercise**

The figure below was generated from an experiment with sample groups 'Mov10_oe', 'Irrel_kd' and 'Mov10_kd'. 

- Do the sample groups separate well?
- Do the replicates cluster together for each sample group?
- Are there any outliers in the data?
- Should we have any other concerns regarding the samples in the dataset?

![heatmap1](../img/pheatmap-1.png)

***
