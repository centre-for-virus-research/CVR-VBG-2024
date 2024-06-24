# NGS/HTS Data Practical
Derek W. Wright, MRC-University of Glasgow Centre for Virus Research
[**Derek.Wright@glasgow.ac.uk**](mailto:Derek.Wright@glasgow.ac.uk)

## Overview
In this practical, we will be exploring the FASTA, multi-FASTA and FASTQ formats.

## Linux Commands
Commands that you need to enter into the terminal window (command line) are presented in a box with a fixed-width font, like this:
```
ls
```
A few tips to remember:
- Use the **tab key** to automatically complete filenames – especially long ones.
- Use the **up arrow** to scroll through your previous commands, it enables you to easily re-run or re-use/adapt old commands.
- **Case Matters** - the following file names are all different:
```
Myfile.txt
MyFile.txt
MYFILE.txt
myfile.txt
my file.txt
my_file.txt
```
Watch out for number 1 being confused with lowercase letter L, and capital letter O being confused with zero 0. 
- **l** = lower case L
- **1** = number one
- **O** = capital letter O
- **0** = zero

Shorthand/wildcard symbols help to save typing:
- **~** is shorthand for your home directory
- **.** is shorthand for the current working directory
- **..** is shorthand for the directory above
- **\*** may be used as a wildcard to match file names

## Setup 
- Login to Windows PC
- Login to alpha2 server via MobaXTerm
```
ssh username@alpha2.cvr.gla.ac.uk
```
```
cd ~ 
```
**~** is shortcut for home directory

## File Formats
### Dataset
```
cp -r /home3/dw73x/Formats .
```
**.** is shortcut for current working directory
```
cd Formats
```
### View the FASTA Format
#### Nucleotide Sequence
```
less single_seq.fasta
```
- Press **space** or **f** to scroll down through the file page by page
- Press **b** to scroll back up a page
- Press **q** to quit

#### Amino Acid Sequence
```
less protein.faa
```
- Sars-CoV-2 spike protein amino acid sequence from NCBI

#### Multi-FASTA Format
```
less BabayanEtAl_sequences.fasta 
```
- Press space or **f** to scroll down through the file page by page
- Press **b** to scroll back up
- Press **q** to quit
```
grep '>' BabayanEtAl_sequences.fasta
```
- Search (*grep*) for lines with the **>** symbol in the file
```
grep '>' BabayanEtAl_sequences.fasta | wc -l
```
- Search (*grep*) for lines with the **>** symbol in the file
- Pipe (**|**) the results in to the next command
- Word count (*wc*) the number of lines (**-l**)

### View the FASTQ Format
```
less reads_R1.fastq
```
- Press **space** or **f** to scroll through the file page by page
- Press **b** to scroll back up a page
- Press **q** to quit

```
grep '@SRR1553467.279000' reads_R1.fastq
```
- Search (*grep*) for lines with string “SRR1553467.279000” (i.e. search for the read with the name *SRR1553467.279000*)

```
grep '@SRR1553467.279000' -A 3 reads_R1.fastq
```
- As a FASTQ read consists of 4 lines, also return the 3 lines after (*-A 3*) 

```
wc –l reads_R1.fastq
```
- Word count (*wc*) the number of lines (**-l**) in the file (lines not reads)
- Divide by 4 to get the number of reads
```
grep '^@SRR1553467' reads_R1.fastq | wc -l
```
- Search (*grep*) for lines beginning (**^**) with the ‘SRR’ symbol in the file *reads.fastq*
- Pipe (**|**) the results on to the next command
- Word count (*wc*) the number of lines (**-l**)
- Number of reads in the file

#### Compressed Files
FASTQ files are often gzipped (compressed) and have *.fastq.gz* extension
Use commands *zcat, zmore, zless, zgrep* to access these compressed files
```
zless 00013_OS_L_NA_S1_R1_001.fastq.gz
```