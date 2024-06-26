# Quality Control and Read Cleaning Practical
Derek W. Wright, MRC-University of Glasgow Centre for Virus Research
[**Derek.Wright@glasgow.ac.uk**](mailto:Derek.Wright@glasgow.ac.uk)

## Overview
In this practical, we will be: 
- performing quality control analysis using FASTQC
- collating the output from FASTQC into a single HTML report with MultiQC
- trimming adapters with Trim Galore

## Dataset
First let’s copy over a folder of data to analyse.
The **-r** flag means copy recursively, i.e. copy the directory and its contents.
```
    cp -r /home3/dw73x/RawReads ~/ 
```
The directory contains FASTQ files from a small run of reads from the Illumina MiSeq System. Some of these samples were subsequently run at much higher output and the data led to the discovery of a new arenavirus published here:
<https://doi.org/10.1128/mra.00095-22>

## FASTQC
First, we will examine the data set, so let’s change our working directory:
```
cd ~/RawReads
```
Next, let's launch the interactive version of FASTQC to examine the quality of the reads:
```
fastqc &
```
This launches FASTQC with the graphical user interface (GUI). 

*Tip:* The ampersand tells the shell to run a program in the background, meaning that you can still enter shell commands while the program continues to run.

From FASTQC:	
1. Select **File > Open** from the menu bar.
1. Navigate to the RawReads directory and select all of the *\*.fastq.gz* files in the directory, holding down the control key to select multiple files.
1. Click the **Open** button.
Each dataset is opened in its own tab. Green ticks in the sidebar for the quality metrics mean that quality control has passed. Orange exclamation marks are warnings. Red crosses are fails.

### FASTQC with MultiQC
You could save each report individually, but an alternative workflow is to run FASTQC from the command line, instead of from the GUI, outputting a FASTQC report for each sample and then collating the reports into a single report using MultiQC.
```
mkdir reports
fastqc -o reports *.fastq.gz
cd reports
multiqc .
```
This generates an HTML report named *multiqc\_report.html*
The report is intended to be hosted on a web server or downloaded to your computer, then viewed in a web browser. 
I have placed a copy of the report for you to view at:
<http://bioinformatics.cvr.ac.uk/multiqc_report.html>

### Trim Galore
Trim Galore is a wrapper around the programs Cutadapt and FASTQC, aimed at providing convenient trimming and quality control without setting complex parameters. 
Trim Galore provides options for various adapters or can attempt to auto-detect adapter sequences. 

There are many options. To view the full set of options:
```
trim_galore --help
```
Useful options include:
- **–-paired** may be used for paired-end reads, which have two files for each sample. 
- **–-fastqc** may be added to additionally run FASTQC in a single step.

Let's trim our paired-end read files:
```
cd ..
trim_galore --paired *.fastq.gz
```
Now let's list the directory contents:
```
ls
```
You should see new trimmed sequence files that have been created by Trim Galore, with names of each file pair ending in  *_val_1.fq.gz* and *_val_2.fq.gz*