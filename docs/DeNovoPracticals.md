## De novo assembly practicals


### [Sreenu Vattipally](https://www.gla.ac.uk/schools/infectionimmunity/staff/sreenuvattipally/)
* MRC-University of Glasgow Centre for Virus Research
* University of Glasgow, G61 1QH
* E-mail: Sreenu.Vattipally@glasgow.ac.uk


In the past decade, next-generation sequencing (NGS) has become an important tool for understanding viral evolution. It also has revolutionised many areas of biological research. NGS platforms can now generate large amounts of sequence data in a relatively short time, making them well-suited for studies of genomes, transcriptomes, and epigenetics.

One of the important applications of NGS is whole-genome sequencing (WGS), which can be used to obtain the complete genome sequence of an organism. However, the primary challenge with WGS is that it is difficult to assemble the genome without a reference. 

De novo assembly is the process of assembling a genome without using any reference. This can be a challenge since it requires correctly assembling millions of short DNA sequences (reads) that cover the entire genome. This approach has been used to discover new viruses, characterise known viruses, and study the evolution of viruses.

There are several steps involved in viral de novo assembly, including, quality control, read trimming, assembly, scaffolding of contigs, and gap filling.



In this practical, we will be using three different assemblers i.e. Spades, IDBA UD and ABYSS to assemble the SARS-CoV-2 genome from Illumina reads. 


Create a directory for the analysis
```
mkdir -p ~/DeNovo/SARS-CoV-2
cd ~/DeNovo/SARS-CoV-2/
```

Copy the data for this practical (SRA ID: SRR21065613) to the current directory and uncompress them.

```
cp /home3/bvv2t/exFiles/FQs/SRR21065613* .
gunzip SRR21065613_*
```


Do a quality check and trimming using trim galore program.

```
trim_galore -q 20 -length 50 --illumina --paired SRR21065613_1.fastq SRR21065613_2.fastq
```

After running trim galore you should see two new files (`SRR21065613_1_val_1.fq` and `SRR21065613_2_val_2.fq`) in your working directory. 

Now create individual directories for Spades, IDBA UD and Abyss along with Reference and Validation folders. 

```
mkdir Spades Abyss IDBA_UD Ref Validate
```

## De novo assembly using Spades  

First, go to the Spades directory you have created above

```
cd ~/DeNovo/SARS-CoV-2/Spades
```

Run spades assembly program with different k-mer sizes.

```
/software/SPAdes-v3.15.5/bin/spades.py -k 21,45,73,101 -1 ../SRR21065613_1_val_1.fq -2 ../SRR21065613_2_val_2.fq -o .
```

Here

- -k : different k-mer sizes    
- -1: First reads file
- -2: Second reads file
- -o: output location. Here we are saving in the current directory(.), we can also specify any name.

The program will take some time depending on the CPU power and available memory to complete the job. If everything goes without errors, the final assembled contigs will be stored in contigs.fasta file. In this file, contigs are saved in a sorted manner, with the longest contig first. Remember, these contigs can be positive or negative strands. We have to check the orientation comparing them with the reference sequence.
    
**TODO: What is the length of the longest contig generated?  

## De novo assembly using IDBA_UD 

Now let us try running IDBA UD. 

```
cd ~/DeNovo/SARS-CoV-2/IDBA_UD/
```

Though it is an assembly program for high throughput reads, IDBA_UD works only with fasta files(not fastq). It will not consider the quality of the reads while assembling. So... we have to convert reads from fastq to fasta and submit them to IDBA_UD.

```
fq2fa --merge ../SRR21065613_1_val_1.fq ../SRR21065613_2_val_2.fq reads.fa
idba_ud --mink=21 --maxk=81 step=10 -r reads.fa -o . --num_threads=4
```

Here

- fq2fa: program to convert reads from fastq to fasta
    
- mink: minimum k-mer size
- maxk: maximum k-mer size
- step: k-mer size increment
- r: input reads
- o: output directory
- num_threads: number of processing threads to run the program

The final results will be stored in contig.fa file in the current directory.

**TODO: Check the length of the longest contig.

## De novo assembly using ABYSS 

Go to the Abyss directory

```
cd ~/DeNovo/SARS-CoV-2/Abyss/
```

Run Abyss assembler

```
abyss-pe k=51 n=4 in='../SRR21065613_1_val_1.fq ../SRR21065613_2_val_2.fq' name=Abyss-51 B=2G
```
    
Here
- k: k-mer size
- n: number of threads to use
- in: input reads
- name: output name

As you notice from above, the abyss assembler works with one k-mer at a time. Since we do not know the optimal k-mer size for our data it is advisable to use various k-mer sizes. Use the below `for loop` to run the program with different k-mers.

```
for k in 41 51 61 71 81; 
	do 
	abyss-pe k=$k n=4 in='../SRR21065613_1_val_1.fq ../SRR21065613_2_val_2.fq' name=Abyss-$k B=2G; 
done
```

This will run abyss with k-mer sizes from 41 to 81 with the increment of 10 and save the output in corresponding files. To get the assembly stats, run

```
abyss-fac *-contigs.fa
```

Now combine all the contigs generated by different kmer sizes

```
cat Abyss*-unitigs.fa > Abyss-contigs.fa
```

**TODO: Check the length of the longest contig.
