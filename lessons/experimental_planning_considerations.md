## Experimental planning considerations

Understanding the steps in the experimental process of RNA extraction and preparation of RNA-Seq libraries is helpful for designing an RNA-Seq experiment, but there are special considerations that should be highlighted that can greatly affect the quality of a differential expression analysis. 

These important considerations include:

1. Number and type of **replicates**
2. Avoiding **confounding**
3. Addressing **batch effects**

We will go over each of these considerations in detail, discussing best practice and optimal design.

## Replicates

Experimental replicates can be performed as technical replicates or biological replicates. **Technical replicates** use the same biological sample to repeat the technical or experimental steps in order to accurately measure technical variation and remove it during analysis. **Biological replicates** use different biological samples of the same condition to measure the biological variation between samples. For differential expression analysis the better the estimates of biological variation, the more precise our estimates of the mean expression levels and the more accurate modeling of our data.


In the days of microarrays, technical replicates were considered a necessity; however, with the advanced RNA-Seq technologies, technical variation is much lower than biological variation and **technical replicates are unneccessary.
