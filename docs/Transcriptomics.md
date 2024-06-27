---
title: "Transcriptomics"
layout: default
rank: 9
---

# RNA-Seq Workshop
## 7th Viral Bioinformatics and Genomics Training Course, Glasgow, UK
#### [Quan Gu](https://www.gla.ac.uk/schools/infectionimmunity/staff/quangu/)  , MRC-University of Glasgow Centre for Virus Research, E-mail: quan.gu@glasgow.ac.uk 

A classical RNA-Seq data processing pipeline contains the following steps:

• Quality control \
• Mapping RNA-Seq reads against a reference genome \
• Visualizing reads and transcript structures \
• Performing differential expression analysis \
• Visualizing differential expression analysis (we will introduce more on DE analysis and visualization at Friday's session by **Srikeerthana Kuchi**) \
• Functional annotation and pathway analysis (we will introduce more detail at Friday session by **Srikeerthana Kuchi**)

In this course, all the data we are using has been subsampled to save time and space.

###  1.	Log on to the CVR server ### 

Open MobaXterm and log on to the CVR-Alpha2 server.

Step 1: Create a working directory and go to your directory using the commands:

```
mkdir MyRNASeq
cd MyRNASeq
```
  
This workshop use single-end strand-specific RNA-Seq reads from six samples (6 fastq files, which have been sub-sampled), located in **/home4/VBG_data/RNASeq**

You will generate soft links to these files in your current directory. 

Condition1 - Mock samples: **Mock01.fastq; Mock02.fastq; Mock03.fastq**

Condition2 - Interferon treated samples: **IFNb01.fastq; IFNb02.fastq; IFNb03.fastq**


Step 2: Generate a soft link to the files, here is an example for one file:

```
ln -s /home4/VBG_data/RNASeq/IFNb01.fastq .
```

We need to make soft links to the reference genome file as well. I have downloaded the genome and GTF files. We could go to RefSeq, Ensembl or UCSC website to download the latest version of the human genome (GRCh38/hg38).
E.g. You could download the files via Ensembl https://ftp.ensembl.org/pub/

```
ln -s /home4/VBG_data/RNASeq/Human.fa .
ln -s /home4/VBG_data/RNASeq/Homo_sapiens.GRCh38.107.gtf .
```

**Task 1**: Check the human reference genome (GRCh38) and its annotation files, and print the last 50 lines of these files. 

###  2. Quality control ### 

The first step of RNA-Seq analysis is Quality control. 
Before we start the analysis, it is always good inspect the quality of the samples. Here we use **FastQC** to visualize the quality of the sequences. To begin, simply run:
```
fastqc *.fastq
```
Check the reads quality from the output HTMl file of FASTQC. 

Before running the RNA-Seq pipeline, we could check the contamination level of the samples. We could use **Kraken** and **Kronaplot** to explore the proportion of reads mapped to bacterial or viral genomes. In this workshop, we will ignore this step. After then we will do trimming after quality control. Trimming includes quality trimming and adapter trimming. In RNA-Seq, we normally do "gentle" trimming to remove the adapter (not to trim the reads too much).

Several trimming programs exist: **Trim_galore**, **cutadapt** , **Prinseq**, **Trimmomatic**,etc.  
In this workshop, we will use **Trim_galore** to trim the reads. 
Note: the default adapter sequence of Illumina and Proton sequencing platforms are different.

The following command will trim with default settings of **Trim_galore**:

```
trim_galore IFNb01.fastq >/dev/null 2>&1
```

**Task 2**: Check the difference between raw data and trimmed data by (1) file size, (2) read number and (3) read quality.


###  3.	Mapping RNA-Seq reads against a reference genome ###  

The next step is to align reads to the genome. The mapping output are BAM files which is a binary version of a SAM file. A SAM file (.sam) is a tab-delimited text file that contains sequence alignment data. This step is most time-consuming step.
In this workshop, we will use **Hisat2** to map the reads to the reference genome, which is a mapping program specifically developed for RNA-Seq analysis.  When doing this at home, remember to build the **Hisat2** index before you run the mapping, which I have already done for you. Now you could directly run the following **Hisat2** command with its default setting:

```
genome='/home4/VBG_data/RNASeq/Human'
hisat2 -x $genome --rna-strandness R -U IFNb01_trimmed.fq -S IFNb01.sam
```

This has produced a SAM file. We need to generate the BAM files as well, which is the binary version of SAM files. After then you could remove the SAM files because they are too big.

 ```
samtools view -bh IFNb01.sam > IFNb01.bam
samtools sort -o IFNb01_sorted.bam IFNb01.bam
samtools index IFNb01_sorted.bam
rm IFNb01.sam
```
**Task 3**: What is the mapping rate against the human genome of this sample? Is it a high rate?

### 4.	Visualizing reads and transcript structures ### 
After getting your provided aligned BAM files, you can visualize the location of the mapped transcripts using visulization software such as: **IGV**, **Tablet**, **Ugene**, etc.

### 5.	Performing differential expression analysis ###
In this workshop, we will use **edgeR** for the differential expression(DE). Here, we omit the step of transcriptome assembly as we don’t want to discover novel DE genes from those in our current genome.
There are two well-known tools which can count the mapped transcripts in the genome. To learn more about how **featureCounts** and **htseq** compare, I have written a blog about it http://bioinformatics.cvr.ac.uk/blog/featurecounts-or-htseq-count/

Here we use **htseq** to get the mapped raw counts of each gene in each sample.

```

htseq-count -s reverse IFNb01_sorted.bam  /home4/VBG_data/RNASeq/Homo_sapiens.GRCh38.107.gtf > countfile
head -n -5 countfile > IFNb01_count_htseq.txt
rm countfile

```

In this course, we don't have enough time to make the count table for all the samples. by ourselves. However, we can use the overall count table that I have prepared for you, which is based on the real data.

Please copy the overall count table file **DE1input.txt** into your own directory. 

```
cp /home4/VBG_data/RNASeq/DE1input.txt  . 
more DE1input.txt
```

After, we can open R and run **edgeR** to carry out DE analysis.

I have written an Rscript called **edgeR** that has all the necessary commands. You can view the code of **edgeR** with the following command:

```
more /home4/VBG_data/RNASeq/edgeR.r
```

Here we use the classical mode of differential expression (DE) analysis in **edgeR**, which assumes all replicates within group are equal. The cut-off the DEGs is Adjust P-value (i.e. Q-value) < 0.05.

You can simply type this command line :

```
R
```

When R opens,
```
BiocManager::install("edgeR")
BiocManager::install("org.Hs.eg.db")
BiocManager::install("EnhancedVolcano")
```
say "yes"
once installed, type 
```
q()
```

then type then following in terminal
```
Rscript /home4/VBG_data/RNASeq/edgeR.r
```

Then you will get the output files: **DEG_edgeR.csv**, **cpm.csv** , **bcvplot.pdf**, **VolcanoPlot.png** and **mdsplot.pdf**. Check the output files and explore what they stand for.

Now let us explain the **edgeR** command here. **edgeR** works on a table of integer read counts, with rows corresponding to genes and columns to independent libraries. First we read the table

```
library(edgeR)
output <- read.table("DE1input.txt",header=T)
output2<-output[,-1]
samplescondition<-c(rep("Mock",3),rep("IFNb",3))
samplesshortname<- c("Mock01","Mock02","Mock03","IFNb01","IFNb02","IFNb03")
rownames(output2)<- output[,1]
colnames(output2)<- samplesshortname
```
After then, we calculate the Count Per Million (CPM) of each gene, which is used as estimated gene expression values.
```
output3<-cpm(output2)
write.csv(output3,file="cpm.csv")
```
We could filter the genes which did not occur frequently. Here we're only keeping a gene if it has a cpm of 1 or greater for at least three samples. This step is optional.
```
keep <- rowSums(cpm(output2)>1) >=3
output2 <- output2[keep,]
```
**edgeR** stores data in a simple list-based data object called a DGEList. This type of object is easy to use because it can be manipulated like any list in R. We can do it in R by specifying the counts and the groups in the function DGEList().
```
d=DGEList(counts=output2,group= factor(samplescondition))
```
**edgeR** is concerned with differential expression analysis rather than with the quantification of expression levels. The calcNormFactors() function in **edgeR** normalizes RNA composition by calculating a set of scaling factors for the library sizes. These scaling factors minimize the log-fold changes between samples for most genes. The default method for computing these scale factors is the trimmed mean of M-values (TMM) between each pair of samples. The product of the original library size and the scaling factor is called the effective library size. This effective library size replaces the original library size in all downstream analyses.
```
d=calcNormFactors(d)
```
We could use MDS plot to explore the relationship among the samples. An MDS is conceptually similar to a PCA. One of the main differences is that it requires input data to be in a different format. As PCA uses raw genotypes and can accommodate data at the individual level, pairwise distances between data points are used for an MDS.
```
plotMDS(d, labels = samplesshortname, col = c("red","blue")[factor(samplescondition)])
```
![](https://github.com/centre-for-virus-research/CVR-VBG-2024/blob/main/images/RNA-Seq-MDSplot.png)

The first major step in analyzing data is to estimate the dispersion parameter for each tag, which measures the degree of inter-library variation for that tag. Estimating the common dispersion provides an understanding of the overall variability across the genome for this dataset.

```
d =estimateCommonDisp(d)
```
Here we use empirical Bayes tagwise dispersions. 

```
d =estimateTagwiseDisp(d)
```
In addition to the common and tagwise disperson of classic model, we can also estimate a generalized linear model (GLM) fit This pipeline splits into two distinct approaches (GLM Likelihood Ratio Test or GLM Quasi-Likelihood F-test) with stricter error rate. Although we only introduce classic model here, you could still read **edgeR** manual if you are interested with GLM model.

plotBCV() plots the tagwise biological coefficient of variation (square root of dispersions) against log2-CPM. 
```
plotBCV(d,ylim=c(0,1.5))
plotBCV(d)
```

![](https://github.com/centre-for-virus-research/CVR-VBG-2024/blob/main/images/RNA-Seq-bcvplot.png)

Once the dispersions are estimated, we can proceed with testing procedures for determining differential expression. The exactTest() function performs tagwise tests using the exact negative binomial test. The test results for the n most significant tags can be conveniently displayed using the topTags() function. By default, Benjamini and Hochberg's algorithm is employed to control the false discovery rate (FDR). We use FDR<5% as the default cut-off.
 
```
de = exactTest(d, pair = c("Mock","IFNb"))
tt = topTags(de, n=nrow(d))
nc = cpm(d, normalized.lib.sizes=TRUE)
rn = rownames(tt$table)
head(nc[rn,order(samplescondition)],5)
deg = rn[tt$table$FDR < .05]
hihi<-tt[tt$table$FDR < .05,]
```
Output the file into CSV file. We will have the information of Gene ID, log2 Fold Change, P-value, FDR, etc.
```
write.csv(hihi$table, file="DEG_edgeR.csv")
```
Now we could plot the top Differential Expressed genes by Volcano plot.
```
png('VolcanoPlot.png')
plot(tt$table$logFC, -10*log10(tt$table$PValue), main="Volcano plot", xlab="logFC", ylab="-10*log(Pval)")
# highlight our DE genes
tol10b.enriched <- tt$table$logFC > 0 & tt$table$FDR < 0.01
points(tt$table$logFC[tol10b.enriched], -10*log10(tt$table$PValue[tol10b.enriched]), col="red")
# identify genes enriched in the control
tol10b.depleted <- tt$table$logFC < 0 & tt$table$FDR < 0.05
points(tt$table$logFC[tol10b.depleted], -10*log10(tt$table$PValue[tol10b.depleted]), col="green")
```

**Task 4**: After running the edgeR code, please check the output files.  How to explore the relationships among samples? How many DE genes do we have? What is the cut-off of the FDR P-value? What are the CPM values?

### 6. Function annotation and pathway analysis ### 
Once we get the differential expression gene list, the next step is to annotate them and
analyse their function and pathways. There are many free online tools available. 
One of the
best-known tools is **David** (https://david.ncifcrf.gov/tools.jsp). 
We could also try **Reactome**
(https://reactome.org), which is highly recommended. 
The user guide of **Reactome** is here (https://reactome.org/userguide/analysis). You could copy and paste the significant DE genes ID list from **DEG_edgeR.csv** and run analysis. 

**Task 5**: Try to find the top 20 pathways of our significant DE genes list.

Alternatively, you could directly run the **Reactome** R script I have written for you. 

You can simply type this command line :

```
R
```

When R opens,
```
BiocManager::install("ReactomePA")
install.packages("stringr", repos='http://cran.us.r-project.org')
Install.packages("ggplot2")
BiocManager::install("clusterProfiler")
install.packages("tidyverse")

```
say "yes"
once installed, type 
```
q()

```
Then please run these code in your terminal:

```
cat DEG_edgeR.csv|sed 's/"//g'| awk 'NR>1' | cut -f1 -d',' > gene.glist
Rscript /home4/VBG_data/RNASeq/Reactome.r hs gene.glist ./ 20
```
Then you will get the output files: **gene.bubble.pdf**, **gene.SigReactome.xls**. The pdf file is the figure of the top 20 pathways enrichment of our significant DE genes. The xls file is the table of details of all the pathways (with P-value < 0.05) of our significant DE genes.  

![](https://github.com/centre-for-virus-research/CVR-VBG-2024/blob/main/images/RNA-Seq-pathway.png)


Here we have some bonus questions:


**Bonus 1**:
Could you use any of visulization tools (**IGV**, **Ugene**, **Tablet**, etc.) to visualize your aligned SAM/BAM file?

**Bonus 2**:
As we know,  **featureCounts** could do a counts table as well. Could you please write your own command for doing it? (The manual: https://subread.sourceforge.net/featureCounts.html. Be aware of strand-specific settings!)

**Bonus 3**:
You have done trimming, references alignment, and features count for the sample **IFNb01.fastq**, could you please also run the same steps on five other samples? You could still do it by command line, but it is better to write it with a BASH script (e.g. by loop, or input arguments).



