---
title: Markdown and RMarkdown
date: July 20, 2022
---




<br><br>


## Table of Contents

- [1. Software Install](#software-install)

	- [1.1 Install ``RMarkdown``](#install-rmarkdown)

	- [1.2 Install ``TinyTeX``](#install-tinytex)

	- [1.3 Install ``Pandoc``](#install-pandoc-document-converter)

- [2. Data for today's exercise](#data-for-todays-exercise)

- [3. Further Reading](#further-reading)

- [4. Creating a RMarkdown Document](#creating-a-rmarkdown-document)




<br><br><br><br><br>
<br><br><br><br><br>
<br><br><br><br><br>
<br><br><br><br><br>
<br><br><br><br><br>
<br><br><br><br><br>
<br><br><br><br><br>


## 1. Software Install

We will be using RStudio to create a RMarkdown document. But before we can do so, we will need to install following software:


<br>

### 1.1 Install ``RMarkdown``

- Using CRAN repository (stable version, but could be slightly stale)

```r
install.packages("rmarkdown")
```

- Development version (cutting edge) from GitHub

```r
library(devtools)

devtools::install_github("rstudio/rmarkdown")
```


<br>

### 1.2 Install ``TinyTeX``

RMarkdown is able to produce LaTeX formatted output (``.tex`` format) but it requires ``TinyTeX`` a very small and easy to maintain TeX distribution.  Installation is straightforward:

```r
install.packages("tinytex")

library(tinytex)

tinytex::install_tinytex()
```


<br>

### 1.3 Install ``Pandoc`` Document Converter

Pandoc is a versatile document converter with numerous formats available including pdf and html. Install it from [https://pandoc.org/installing.html](https://pandoc.org/installing.html).

<br><br>

## 2. Data for today's exercise

This exercise uses a population genomic data set from study of two hybridizing *Populus* species. One of the goals of this study was to uncover signatures of natural selection in hybrid individuals. In order to do this, a genetic differentiation index called ``Fst`` is calculated between populations for two sets of genetic loci: those that have been determined to be under the effect of natural selection and those that are presumed neutral (i.e. no selection). We will be making a couple of figures from this data.

- ``sig.fst`` contains data for SNPs under natural selection

-  ``neu.fst`` contains data for SNPs presumed neutral 

- ``pval_counts.txt`` contains data on bootstrapping


If you run ``git pull`` in this repository's clone directory on your computer, you should then see the ``data`` folder under this week's section.

<br><br>






## 3. Further Reading 

- [R Markdown: A Definitive Guide](https://bookdown.org/yihui/rmarkdown) by Yihui Xie, J. J. Allaire, Garrett Grolemund

- [RMarkdown Tutorial by Karl Broman](https://kbroman.org/knitr_knutshell/pages/Rmarkdown.html)



<br><br>


## 4. Creating a RMarkdown Document

If you were not in attendance for this session, follow the rest of this guide to create your document:

<br>

### 4.1 Create Document Preamble


---
title: FSTs in hybrid poplars
author: Vikram Chhatre
date: 07/20/2022
output: html\_document
---


<br>


### 4.2 Create a Fst Density Plot

**Note:** Remember to enclose all R code between backticks in the following manner:

``
```{r}
plot()
```
``


- Import libraries and data into R

```{r}
library(ggplot2)
library(reshape2)

sig.fst <- read.table("sig.fst", header=F)
neu.fst <- read.table("neu.fst", header=F)

``` 

- Format and merge data

```{r}
sig <- data.frame(x=sig.fst$V2, label=rep("Significant (1764 SNPs)", 1764))

neu <- data.frame(x=neu.fst$V2, label=rep("Neutral (1912 SNPs)", 1912))

fst <- rbind(sig, neu)

```

- Make density plot


```{r}
ggplot(fst, aes(x, y=..density.., fill=label)) + xlim(0,1) +
	geom_density(color="black", alpha=0.7) +
	scale_fill_manual(values=c("orange", "lightgray")) +
	labs(x=expression(paste("Admixture Zone"~italic(F)[ST])), y="Density (loci)",
		title=expression(paste("(b) "~italic(F)[ST]~" in "~italic("P. angustifolia")~" derived alleles"))) +
	theme(legend.position=c(0.8,0.9), legend.title=element_blank())

```
<br>

### 4.3 Reproducibility of results

- Read the data file

```{r}
pct <- read.table("pval_counts.txt", header=T)
```


- Assign colors to numbers

```{r}
pct$fill <- ifelse(pct$counts_pvalzero == 0, "lightgray",
		ifelse(pct$counts_pvalzero == 100, "orange", "black"))
```


- Draw a histogram to show reproducibility

```{r}
ggplot(pct, aes(counts_pvalzero, fill=fill)) +
	geom_histogram(binwidth=1) +
	scale_fill_manual(values=c("black", "gray", "orange"),
		labels=c("Variabily Sig. (3847 SNPs)", "Never Sig. (1912 SNPs)", "Always Sig. (1764 SNPs)")) +
		labs(x="Num. of Introgress Replicates", y="Significantly introgressed loci",
			title="(a) Reproducibility of Introgress results") +
	theme(legend.position=c(0.8,0.9), legend.title=element_blank(), legend.text=element_text(size=8))

```


<br><br>

### 4.4 Compile the Document

- If you are working within RStudio, simply click the ``knit`` button.  This should generate an HTML file that RStudio will open up in a browser instance.

- If you are working in a R terminal, follow these prompts:


```r

library(RMarkdown)

rmarkdown::render("fst.Rmd", "html_document")

```



<br><br>

### 4.5 Example Output

- If your document was compiled correctly, you should see output like [this file](data/fst.html). 
