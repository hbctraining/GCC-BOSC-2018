# Experimental planning considerations

Understanding the steps in the experimental process of RNA extraction and preparation of RNA-Seq libraries is helpful for designing an RNA-Seq experiment, but there are special considerations that should be highlighted that can greatly affect the quality of a differential expression analysis. 

These important considerations include:

1. Number and type of **replicates**
2. Avoiding **confounding**
3. Addressing **batch effects**

We will go over each of these considerations in detail, discussing best practice and optimal design.

## Replicates

Experimental replicates can be performed as **technical replicates** or **biological replicates**. 

<img src="../img/replicates.png" width="400">

*Image credit: [Klaus B., EMBO J (2015) **34**: 2727-2730](https://dx.doi.org/10.15252%2Fembj.201592958)*

- **Technical replicates:** use the same biological sample to repeat the technical or experimental steps in order to accurately measure technical variation and remove it during analysis. 

- **Biological replicates** use different biological samples of the same condition to measure the biological variation between samples. 

In the days of microarrays, technical replicates were considered a necessity; however, with the current RNA-Seq technologies, technical variation is much lower than biological variation and **technical replicates are unneccessary**.

In contrast, **biological replicates are absolutely essential**. For differential expression analysis, the more biological replicatse, the better the estimates of biological variation and the more precise our estimates of the mean expression levels. This leads to more accurate modeling of our data and identification of more differentially expressed genes.

<img src="../img/de_replicates_img.png" width="500">

*Image credit: [Liu, Y., et al., Bioinformatics (2014) **30**(3): 301–304](https://doi.org/10.1093/bioinformatics/btt688)*

As the figure above illustrates, **biological replicates are of greater importance than sequencing depth**. The figure shows the relationship between sequencing depth and number of replicates on the number of differentially expressed genes identified [[1](https://academic.oup.com/bioinformatics/article/30/3/301/228651/RNA-seq-differential-expression-studies-more)]. Note that an **increase in the number of replicates tends to return more DE genes than increasing the sequencing depth**. Therefore, generally more replicates are better than higher sequencing depth, with the caveat that higher depth is required for detection of lowly expressed DE genes and for performing isoform-level differential expression. 

Replicates are almost always preferred to greater sequencing depth for bulk RNA-Seq. However, guidelines depend on the experiment performed and the desired analysis. Below we list some general guidelines for replicates and sequencing depth to help with experimental planning:

- **General gene-level differential expression:**

  - ENCODE guidelines suggest 30 million SE reads per sample (stranded).
  
  - 15 million reads per sample is often sufficient, if there are a good number of replicates (>3). 

  - Spend money on more biological replicates, if possible.

- **Gene-level differential expression with detection of lowly-expressed genes:**
  
  - Similarly benefits from replicates more than sequencing depth.

  - Sequence deeper with at least 30-60 million reads depending on level of expression (start with 30 million with a good number of replicates). 
  
- **Isoform-level differential expression:**

  - More depth (> 60 million reads per sample) and paired-end reads are suggested.

  - Choose biological replicates over paired/deeper sequencing.

  - Perform careful QC of RNA quality. Be careful to use high quality preparation methods and restrict analysis to high quality RIN # samples.  
  
- **Other types of RNA analyses (intron retention, small RNA-Seq, etc.):** 
  
  - Different recommendations depending on the analysis.
  
  - Almost always more biological replicates are better!
  
  ## Confounding
  
  
