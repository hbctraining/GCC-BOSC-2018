## Normalization

The first step in the DE analysis workflow is count normalization, which is necessary to make accurate comparisons of gene expression between samples.

<img src="../img/deseq_workflow_normalization.png" width="200">

The counts of mapped reads for each gene is proportional to the expression of RNA ("interesting") in addition to many other factors ("uninteresting"). Normalization is the process of scaling raw count values to account for the "uninteresting" factors. In this way the expression levels are more comparable between and/or within samples.

The main factors often considered during normalization are:
 
 - **Sequencing depth:** Accounting for sequencing depth is necessary for comparison of gene expression between samples. In the example below, each gene appears to have doubled in expression in sample 2, however this is a consequence of sample 2 having double the sequencing depth.
 
    <img src="../img/sequencing_depth.png" width="400">
 
 - **Gene length:** Accounting for gene length is necessary for comparing expression between different genes within the same sample. The number of reads mapped to a longer gene can appear to have equal count/expression as a shorter gene that is more highly expressed. 
 
    <img src="../img/length_of_gene.png" width="400">
 
 - **RNA composition:** A few highly differentially expressed genes can skew some types of normalization methods. Accounting for RNA composition is recommended for comparison of expression between samples, and is particularly important when performing differential expression analyses [[1](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-10-r106)]
    
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

### TPM (recommended)
In contrast to RPKM/FPKM, TPM-normalized counts normalize for both sequencing depth and gene length, but have the same total TPM-normalized counts per sample. Therefore, the normalized count values are comparable both between and within samples.

> *NOTE:* [This video by StatQuest](http://www.rna-seqblog.com/rpkm-fpkm-and-tpm-clearly-explained/) shows in more detail why TPM should be used in place of RPKM/FPKM if needing to normalize for sequencing depth and gene length.

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

Principal Component Analysis (PCA) is a technique used to emphasize variation and bring out strong patterns in a dataset (dimensionality reduction). For details regarding the calculations performed for PCA, we encourage you to explore [StatQuest's video](https://www.youtube.com/watch?v=_UVHneBUBW0). 
