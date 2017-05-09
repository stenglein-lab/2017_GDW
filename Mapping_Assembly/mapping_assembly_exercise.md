# Mapping and Assembly

## In this exercise, we will de novo assemble a viral genome from a publically available dataset and will use read mapping to validate our assembly.  We will:

* Download a dataset from the SRA
* Convert the dataset from SRA -> FASTQ format
* Find and download a genome sequence from NCBI
* Create a bowtie index from the genome sequence (necessary to map reads to it)
* Map reads in the dataset to the genome 
* De-novo assemble non-mapping reads (to assemble viral genome)
* Map reads to the draft viral genome to validate assembly

Other/todo:

* FASTQC
* read trimming
* QUAST


## Download an SRA dataset

We will download one of the NGS datasets reported in [this paper](http://journals.plos.org/plospathogens/article?id=10.1371/journal.ppat.1004900)

This dataset was generated by performing shotgun sequencing of total RNA from the liver of a boa constrictor that was diagnosed with [inclusion body disease](https://en.wikipedia.org/wiki/Inclusion_body_disease). The dataset is composed of reads from host RNAs and from viral RNAs.  

First, let's setup a directory (folder) in which to work.  Lines that start with a `#` symbol are comment lines and are just for your information.  Open the terminal app on your laptop, then type these commands:

```
# change (move) to your home directory, if not already there
cd

# make a new directory
mkdir boa_sra

# move to that directory
cd boa_sra

# double check you are in the directory you think you are:
pwd
```

To get the dataset, open a browser and enter this address, the pubmed page for the dataset's paper:

https://www.ncbi.nlm.nih.gov/pubmed/25993603

Scroll down and find the 'Related information' section of the bottom right of the page.  Click on the SRA link.  This shows the 146 datasets associated with this paper.  Search for `snake_7`.  Note that the reads in this dataset are already trimmed.  Note the run # (SRR #) for this dataset: SRR1984309

To download the dataset to your computer, you can run this command from your terminal window:

```
curl -O ftp.ncbi.nlm.nih.gov:/sra/sra-instant/reads/ByRun/sra/SRR/SRR198/SRR1984309/SRR1984309.sra
```

*5 sec over wifi*

See [this page](http://www.ncbi.nlm.nih.gov/books/NBK158899/) for more information about how this URL was determined.  Here is a [tool](https://github.com/stenglein-lab/stenglein_lab_scripts/blob/master/download_sra_data) for downloading SRA data that will automate this process.

Confirm that you downloaded the file:

```
# you should see a file named SRR1984039.sra that is 22 Mb
ls -lh
```


## Convert SRA->FASTQ

SRA datasets arrive in SRA format.  We need them to be in a more usable format, like [FASTQ](https://en.wikipedia.org/wiki/FASTQ_format).   

We will use the [SRA toolkit](https://www.ncbi.nlm.nih.gov/books/NBK158900/) to convert from SRA to FASTQ.

```
# fastq-dump converts SRA->FASTQ
fastq-dump --split-files SRR1984309.sra

# rename files using the mv (move) command
mv SRR1984309_1.fastq SRR1984309_R1.fastq
mv SRR1984309_2.fastq SRR1984309_R2.fastq
```

*~1 sec*

## Download the boa constrictor (mtDNA) genome.

The dataset we downloaded is from boa constrictor liver RNA.  We are going to map the reads in the dataset to the boa constrictor mtDNA genome sequence to demonstrate read mapping.  We'll use the [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) mapper.

#### First we need to actually get the genome sequence to which we will map:

* Goto NCBI Taxonomy database: https://www.ncbi.nlm.nih.gov/taxonomy
* Search for "boa constrictor"
* Click on boa constrictor link
* Click on genome (1) link
*
* Click on 'See also 1 organelle- and plasmid-only records matching your search/
* Click on 'NC_007398.1' RefSeq link

#### As is usually the case in bioinformatics, there is more than one way to do what we want:

* Option 1: Download from website using browser
  * download sequence from NCBI website
  * Send->complete record->file->format[fasta]
  * Move this FASTA file to your ~/boa_sra directory using the Finder or the command line

* Option 2: download the sequence in Geneious.  
  * Copy the accession # from your browswer page.  
  * Open Geneious.  Goto the NCBI->Nucleotide section.  
  * Search for the accession (NC_007398.1) .  
  * Create a new folder in Geneious.  
  * Drag the boa mtDNA sequence to this new folder.
  * Note the nice annotation.
  * Export the sequence in FASTA format.  File->Export->Selected Documents->Fasta sequences/alignment format.  Click through options.  

#### Create a bowtie index

Now we will create a bowtie2 index from our boa mtDNA sequence.  Indexing pre-processes a sequence to make it faster to map to later.

In your terminal window run these commands:

```
# confirm that the fasta file is there
ls -lh    # should see: NC_007398.fasta

# run bowtie2-build to make index
# this command takes 2 arguments: 
# (1) the name of the fasta file containing the sequence(s) you will index
# (2) the name of the index (can be whatever you want)

bowtie2-build NC_007398.fasta NC_007398

# confirm that you built the index.  You should see a bunch of files named ending in bt2, like NC_007398.3.bt2
ls -lh
```

#### Mapping reads in our dataset to the bowtie index

#### Download the dolphin genome

We are going to map the reads in our dataset to the dolphin genome.  First, we need to *find* the dolphin genome.  As usual, there are few ways we could go about this:

1. navigate through the NCBI [Genome database](https://www.ncbi.nlm.nih.gov/genome/)
2. navigate through another genome database, like [Ensembl](http://www.ensembl.org/index.html) or [UCSC](https://genome.ucsc.edu/) 
3. google 'dolphin genome sequence'  (not a terrible way to do it)

We will go through the NCBI Genome database.  Navigate to:

https://www.ncbi.nlm.nih.gov/genome/

Search for `dolphin`.  Scroll down until you see the record for *Tursiops truncatus* (the bottlenosed dolphin).  Click on that link.

From this overview page, there are a number of paths to the actual genome sequence.  At the top of the page are links to download sequences in FASTA format.  Hover over the link to download the genome sequence in FASTA format.  Note that this link points to this URL:

ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/151/865/GCF_000151865.2_Ttru_1.4/GCF_000151865.2_Ttru_1.4_genomic.fna.gz

This is an FTP link, meaning that we can download this file directly from the command line using a utility like [curl](https://en.wikipedia.org/wiki/CURL):

make sure you're in the right directory:
```
cd ~/boa_sra
```

download the dolphin genome sequence using the curl utility
```
curl -O ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/151/865/GCF_000151865.2_Ttru_1.4/GCF_000151865.2_Ttru_1.4_genomic.fna.gz
```

** We don't actually want to continue this with the whole genome sequence because it will take a long time to download and index **

Press `ctrl-C` on your keyboard to cancel the curl download


* 10 minutes over a DSL line*

confirm you've downloaded the genome sequence
you should see a file named GCF_000151865.2_Ttru_1.4_genomic.fna.gz 
```
ls -lh 
```

the .gz file extension means this file is gzipped (compressed)
decompress it using gunzip
```
gunzip GCF_000151865.2_Ttru_1.4_genomic.fna.gz 
```

* 1 minute on macbook *

the file should now be named GCF_000151865.2_Ttru_1.4_genomic.fna
```
ls -lh
```

Note that the file went from ~700 Mb to ~2.4 Gb after decompression.  

#### Download the dolphin mtDNA

** We could align reads to the entire dolphin genome, but that would take a time longer than we have for this workshop **
** Instead, we'll align reads to the dolphin mtDNA genome **

Return to the NCBI Genome bottlenosed dolphin page:

https://www.ncbi.nlm.nih.gov/genome/769

Under the Replicon Info section, note the link to the mitochondrial genome: NC_012059.1.  Click that link to go to the Genbank nucleotide record for this genome.

#### As is usually the case in bioinformatics, there is more than one way to do what we want:

* Option 1: Download from website using browser
  * Send->complete record->file->format[fasta]
  * Move this FASTA file to your ~/boa_sra directory using the Finder or the command line

* Option 2: download the sequence in Geneious.
  * Copy the accession # from your browswer page.
  * Open Geneious.  Goto the NCBI->Nucleotide section.
  * Search for the accession (NC_007398.1) .
  * Create a new folder in Geneious.
  * Drag the boa mtDNA sequence to this new folder.
  * Note the nice annotation.
  * Export the sequence in FASTA format.  File->Export->Selected Documents->Fasta sequences/alignment format.  Click through options.o


#### Create a bowtie index of the dolphin mtDNA genome

Now we will create a bowtie2 index from our dolphin mitochondrial genome sequence.  Indexing pre-processes a sequence to make it faster to map to later.

In your terminal window run these commands:

confirm that the fasta file is there
```
ls -lh    # should see: NC_012059.fasta
```

run bowtie2-build to make index
this command takes 2 arguments: 
(1) the name of the fasta file containing the sequence(s) you will index
(2) the name of the index (can be whatever you want)

```
bowtie2-build NC_012059.fasta NC_012059
```
* 1 sec on macbook *

confirm that you built the index.  You should see a bunch of files named ending in bt2, like NC_012059.3.bt2
```
ls -lh
```



#### Map reads to dolphin mtDNA genome

[Bowtie2](http://www.nature.com/nmeth/journal/v9/n4/full/nmeth.1923.html) was developed as a fast short-read mapper.  It aligns short NGS reads to a reference sequence.  Bowtie2 has a nice manual that explains how to use it and what the various parameters mean.

Note that there are a variety of other good read mapping tools, including [HiSat](https://ccb.jhu.edu/software/hisat2/index.shtml), [gmap/gsnap](http://research-pub.gene.com/gmap/), and [STAR](https://github.com/alexdobin/STAR).



```
bowtie2 -x dolphin_mtDNA_bt_index -q -1   -2 --qc-filter --no-unal --threads 4 -S ${output_prefix}.sam
```

Let's deconstruct this command line a little
```
bowtie2 -x dolphin_mtDNA_bt_index -q -1   -2 --qc-filter --no-unal --threads 4 -S ${output_prefix}.sam

```




#### Create a bowtie index of the dolphin genome

Now we will create a bowtie2 index from our dolphin genome sequence.  Indexing pre-processes a sequence to make it faster to map to later.

In your terminal window run these commands:

confirm that the fasta file is there
```
ls -lh    # should see: NC_007398.fasta
```

run bowtie2-build to make index
this command takes 2 arguments: 
(1) the name of the fasta file containing the sequence(s) you will index
(2) the name of the index (can be whatever you want)

```
bowtie2-build NC_007398.fasta NC_007398
```

confirm that you built the index.  You should see a bunch of files named ending in bt2, like NC_007398.3.bt2
```
ls -lh
```







#### De-novo assembly of non-mapping reads





