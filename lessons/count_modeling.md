---
title: "Count modeling and hypothesis"
author: "Meeta Mistry, Radhika Khetani, Mary Piper"
date: "May 12, 2017"
---

Approximate time: 30 minutes

## Learning Objectives 

* Explain why negative binomial distribution is used to model RNA-seq count data
* The mean-variance relationship and how that changes as we add replicates
* Tools for differential expression analysis 
	* DESeq2
	* edgeR
* Modeling transcript level data with sleuth
	*Bootstrap and technical variation
* Hypothesis testing for pairwise comparisons (e.g wald test)
* Hypothesis testing for multiple levels/time series (e.g. LRT)


## RNA-seq count distribution

To determine the appropriate statistical model, we need information about the distribution of counts. To get an idea about how RNA-seq counts are distributed, we can plot the counts for a single sample:

<img src="../img/deseq_counts_distribution.png" width="400">

If we **zoom in close to zero**, we can see that there are a large number of genes with counts of zero:

<img src="../img/deseq_counts_distribution_zoomed.png" width="400">

These images illustrate some common features of RNA-seq count data:

* a **low number of counts associated with a large proportion of genes**
* a long right tail due to the **lack of any upper limit for expression**
* large dynamic range

> **NOTE:** The log intensities of the microarray data approximate a normal distribution. However, due to the different properties of the of RNA-seq count data, such as integer counts instead of continuous measurements and non-normally distributed data, the normal distribution does not accurately model RNA-seq counts [[1](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3541212/)].
> 
 
 
### Modeling count data

#### Binomial distribution

Count data is often modeled using the **binomial distribution**, which can give you the **probability of getting a number of heads upon tossing a coin a number of times**. However, not all count data can be fit with the binomial distribution. The binomial is based on discrete events and used in situations when you have a certain number of cases.

#### Poisson distribution

When **the number of cases is very large (i.e. people who buy lottery tickets), but the probability of an event is very small (probability of winning)**, the **Poisson distribution** is used to model these types of count data. The Poisson is similar to the binomial, but is based on continuous events. [Details provided by Rafael Irizarry in the EdX class.](https://youtu.be/fxtB8c3u6l8)

**With RNA-Seq data, a very large number of RNAs are represented and the probability of pulling out a particular transcript is very small**. Thus, it would be an appropriate situation to use the Poisson distribution. However, a unique property of this distribution is that the mean == variance. 

#### Mean versus Variance

In the figure below we have plotted the mean against the variance for three replicate samples in a study. Each data point represents a gene and the red line represents x = y. 

<img src="../img/deseq_mean_vs_variance.png" width="600">

There's two things to note here:

1. The variance across replicates tends to be greater than the mean (red line), especially for genes with large mean expression levels. 
2. For the lowly expressed genes we se quite a bit of scatter. We usually refer to this as "heteroscedasticity". That is, for a given expression level we observe a lot of variation in the amount of variance. 

*This is a good indication that our data do not fit the Poisson distribution.* If the proportions of mRNA stayed exactly constant between the biological replicates for each sample class, we could expect Poisson distribution (where mean == variance). Alternatively, if we continued to add more replicates (i.e. > 20) we should eventually see the scatter start to reduce and the high expression data points move closer to the red line. So in theory, of we had enough replicates we could use the Poisson.

However, in practice a large number of replicates can be either hard to obtain (depending on how samples are obtained) and/or can be unaffordable. It is more common to see datasets with only a handful of replicates (~3-5) and reasonable amount of variation between them. The model that fits best, given this type of variability between replicates, is the Negative Binomial (NB) model. Essentially, **the NB model is a good approximation for data where the mean < variance**, as is the case with RNA-Seq count data.


> **NOTE:** If we use the Poisson this will underestimate variability leading to an increase in false positive DE genes.






