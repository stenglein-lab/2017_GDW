# Examining raw data: Dolphin Adenovirus Identification
In this exercise, we will process raw sequencing data from a bottlenose dolphin (*Tursiops truncatus*) fecal sample

The general steps we will perform are:
- Download the raw sequence data from the SRA archive
- Convert to conventional FASTQ format
- Analyze quality metrics using FASTQC
- Trim low quality sequences and adapter sequences
- Screen for vector contamination
- Count kmers in the trimmed sequence data and plot histogram

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
# Find the folder containing potential Illumina Adapters
ls
cat path/to/Trimmomatic-0.36/adapters/*.fa

# Make a new fasta file of concatenated adapters
cat path/to/Trimmomatic-0.36/adapters/*.fa > all-adapters.fa

# Correct several issues for proper Fasta formatting (any text editor) or
vim all-adapters.fa

# Trim Sequences
java -jar ../Trimmomatic-0.36/trimmomatic-0.36.jar \
   PE \
   -threads 2 \
   -phred33 \
   ERR1938563_1.fastq.gz \
   ERR1938563_2.fastq.gz \
   ERR1938563_1.trimmed.fastq.gz \
   ERR1938563_1.trimmed.SE.fastq.gz \
   ERR1938563_2.trimmed.fastq.gz \
   ERR1938563_2.trimmed.SE.fastq.gz \
   ILLUMINACLIP:all-adapters.fa:2:30:7 \
   LEADING:20 \
   TRAILING:20 \
   SLIDINGWINDOW:4:20 \
   AVGQUAL:20 \
   MINLEN:100
```
Questions:
- How many reads were trimmed?
- How many are still paired?
- Has the quality improved?  Repeat FastQC Analysis above with trimmed reads
Notes:
- Correct for path to trimmomatic.jar

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
Did you find any contamination?

## Step 5:  Count kmers
Here we will count the frequency of various k-mers (i.e., 31-mer) using the tool [DSK](https://github.com/GATB/dsk).
The analysis of k-mers is used in a variety of applications, including estimating genome size, correcting sequencing errors, identifying repetitive elements, etc..
```
# Check dsk usage
dsk -h

# Count 31-mers
dsk \
   -file ERR1938563_1.trimmed.fastq.gz,ERR1938563_2.trimmed.fastq.gz \
   -kmer-size 31 \
   -nb-cores 2 \
   -abundance-min 1 \
   -max-memory 1000 \
   -out k31.counts

# Unfortunately, the output is compressed.
# Parse the output
dsk2ascii \
   -file k31.counts.h5 \
   -out k31.table

# Make a histogram input table
../dsk-v2.2.0-Source/build/ext/gatb-core/bin/h5dump \
   -y \
   -d histogram/histogram \
   k31.counts.h5 | \
   grep "^\ *[0-9]" | \
   tr -d " " | \
   perl -ne 's/,\n/\t/; print' > k31.histogram.tsv
   # This can be plotted in R

# Or to plot with gnuplot
../dsk-v2.2.0-Source/build/ext/gatb-core/bin/h5dump \
   -y \
   -d histogram/histogram \
   k31.counts.h5 | \
   grep "^\ *[0-9]" | \
   tr -d " " | \
   paste - - | \
   gnuplot -p -e 'set term png; set logscale y; plot  "-" with lines lt -1' > k31.png
```
Now
- Repeat at kmer sizes of 15, 21, 27
- Where may we recommend an error correction threshold?

