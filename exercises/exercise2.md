# Exercise 2: Dolphin Adenovirus Identification
In this exercise, we will process raw sequencing data from a bottlenose dolphin (*Tursiops truncatus*) fecal sample and align, or map, these sequencing reads to an adenovirus reference genome.

The general steps we will perform are:
- Download the raw sequence data from the SRA archive
- Convert to conventional FASTQ format
- Analyze quality metrics using FASTQC
- Trim low quality sequences and adapter sequences
- Screen for vector contamination NOTE: I may skip this section
- Download and index the adenovirus reference genome
- Map the trimmed reads to both genomes
- Assess the mapping results

## Step 1:  Downloading the raw sequence data
The dataset is described in BioProject [PRJEB14687](https://www.ncbi.nlm.nih.gov/bioproject/?term=PRJEB14687)

First, let's setup a directory (folder) in which to work. Lines that start with a # symbol are comment lines and are just for your information. Open the terminal app on your laptop, then type these commands:
```
# Move to home folder
cd ~

# Make a new folder for this exercise
mkdir DOLPH_VIRUS

# Move into the new folder
cd DOLPH_VIRUS

# Check your current location in the file system (or "Path")
pwd
echo "$PWD"
```

Now, lets get our raw sequencing data!  GenBank's Sequence Read Archive (SRA) is the database where a majority of raw sequencing data for projects is stored.  These data are stored as a series of "Experiments" and "Runs", each with an accession number.  The acession number we want is ERR1938563. Sequencing data are stored in a highly compressed format ending in .sra.  We need to download this data and convert to the conventional FASTQ format.
```
# The sratoolkit from NCBI has a program "fastq-dump" that can perform these tasks and more
fastq-dump \
   --split-files \
   --gzip \
   --origfmt \
   --readids \
   ERR1938563
```
The "\\" at the end of the line tells the computer that the command will continue onto the next line.  This notation can help make really long command look cleaner and easier to understand.
Here is a description of the parameters in the above command:
- --split-files : split the sequences into two files, one with forward reads (mates), the second with reverse mates.
- --gzip : compress the output FASTQ files using gzip.
- --origfmt : Keep the original sequence name in any way.
- --readids : Appends a read id (1 for forward, 2 for reverse) to the end of the sequence name.

If you forget how to use the "fastq-dump" command, you can check its usage with:
```
fastq-dump -h
```
The result should be two files:
- ERR1938563_1.fastq.gz
- ERR1938563_2.fastq.gz
```
# Check the files that are in the present folder
ls
```

## Step 2:  Assess sequence quality using FASTQC
FASTQC is a useful tool for visualizing the distribution of basic quality metrics such as length, quality, and potential contamination.
The program has an easy-to-use graphical interface, or can be run from the command line. For example:
```
fastqc ERR1938563_1.fastq.gz
```
For our purposes, we will use the graphical user interface.

## Step 3:  Trim low quality bases and sequences using TRIMMOMATIC
Now we will use the software TRIMMOMATIC to perform a series of trimming procedures on the raw sequence data.
We will run the command below first, and discuss what is happening while it runs.
```
java -jar ../Trimmomatic-0.36/trimmomatic-0.36.jar \
   PE \
   -threads 1 \
   -phred33 \
   ERR1938563_1.fastq.gz \
   ERR1938563_2.fastq.gz \
   ERR1938563_1.trimmed.fastq.gz \
   ERR1938563_1.trimmed.SE.fastq.gz \
   ERR1938563_2.trimmed.fastq.gz \
   ERR1938563_2.trimmed.SE.fastq.gz \
   ILLUMINACLIP:path/to/adapters.fa:2:30:7 \
   LEADING:20 \
   TRAILING:20 \
   SLIDINGWINDOW:4:20 \
   AVGQUAL:20 \
   MINLEN:100
```
Notes to self:
- Correct for path to trimmomatic.jar
- Create adapter.fasta file and put correct path

## Step 4:  Screen for vector contamination using UNIVEC database
An optional step is to screen the reads for additional contaminating sequences using NCBI's [UNIVEC database](https://www.ncbi.nlm.nih.gov/tools/vecscreen/univec/).
From the website linked above:
"In addition to vector sequences, UniVec also contains sequences for those adapters, linkers, and primers commonly used in the process of cloning cDNA or genomic DNA. This enables contamination with these oligonucleotide sequences to be found during the vector screen."
```
# Let us first uncompress the trimmed sequencing reads and convert to FASTA format
gunzip -c ERR1938563_1.trimmed.fastq.gz | seqtk seq -A - > ERR1938563_1.trimmed.fasta
gunzip -c ERR1938563_1.trimmed.fastq.gz | seqtk seq -A - > ERR1938563_2.trimmed.fasta

# Download the UniVec database (FASTA format)
curl -O ftp://ftp.ncbi.nlm.nih.gov/pub/UniVec/UniVec_Core

# Build a blast database
makeblastdb \
   -in UniVec_Core \
   -title UNIVEC \
   -out UNIVEC \
   -dbtype nucl

# Blast the forward and reverse sequencing reads
blastn \
   -query ERR1938563_1.trimmed.fasta \
   -db UNIVEC \
   -num_threads 2 \
   -outfmt 6 \
   -out forward-contaminants.tsv
blastn \
   -query ERR1938563_2.trimmed.fasta \
   -db UNIVEC \
   -num_threads 2 \
   -outfmt 6 \
   -out reverse-contaminants.tsv
```


## Step 5: Mapping to the reference genome
In this step we will align the trimmed sequencing reads to the reference genome.  To do this we first need to download the reference genome which can be found on GenBank as accession [LT841149.1](https://www.ncbi.nlm.nih.gov/nuccore/LT841149.1)
```
# Move and rename the reference genome file
mv ~/Downloads/sequence.fasta LT84119.fasta
```
Now index the reference genome using bowtie2
```
bowtie2-build -f LT841149.fasta LT841149
```

Finally, we are ready to map the reads to the reference genome
```
# Uncompress the trimmed sequencing reads to make it easier on bowtie2 if not uncompressed previously.
gunzip ERR1938563_1.trimmed.fastq.gz
gunzip ERR1938563_2.trimmed.fastq.gz

# Run the mapping
bowtie2 \
   -q \
   --phred33 \
   --very-sensitive \
   -t \
   -p 1 \
   -x LT841149 \
   -1 ERR1938563_1.trimmed.fastq \
   -2 ERR1938563_2.trimmed.fastq > mapped.sam
```
Here is a description of the parameters in the above command:
- -q : the input sequences are in the fastq format
- --phred33 : the quality scores are in the "Sanger" or "Illimuna 1.9+" format
- --very-sensitive : use strict alignment parameters, slower
- -p 1 : use 1 thread
- -x : basename of the reference genome index
- -1 : forward read file
- -2 : reverse read file
- \> mapped.sam : write the output to the file "mapped.sam"

## Step 6: Assessing the mapping results
It is useful to perform some checks and visualization of the mapping results.  As usual, there are many ways to do this.  Here we will use the samtools package.
```
# Produce a file of mapping summary statistics
samtools stats mapped.sam > mapped.stats

# Make a series of plots to visualize mapping statistics
../samtools-1.4/misc/plot-bamstats -p mapped  mapped.stats
```
Notes: Make sure plot-bamstats gets installed (part of samtools package).
Notes: Make sure gnuplot get installed (brew install gnuplot)
The results are summarized in the mapped.html file which can be opened using a web browser.



## To be continued.....

