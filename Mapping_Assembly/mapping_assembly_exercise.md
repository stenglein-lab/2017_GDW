## Mapping and Assembly Exercise

GDW 2017
---

### In this exercise, we will learn how to map reads to a genome, de novo assemble a viral genome, and use read mapping to validate our assembly.  We will:

* Create a bowtie index from the genome sequence (necessary to map reads to it)
* Map reads in the dataset to the genome 
* De-novo assemble non-mapping reads (to assemble the viral genome)
* Map reads to the draft viral genome to validate assembly


### Create a bowtie index from the boa constrictor mitochondrial genome sequence

We will map reads in the SRA dataset that we downloaded yesterday to the boa constrictor mtDNA sequence that we also downloaded yesterday.  

Read mapping tools map reads very quickly using pre-built indexes of the reference sequence(s) to which reads will be mapped.  We'll use the [Bowtie2](http://www.nature.com/nmeth/journal/v9/n4/full/nmeth.1923.html) mapper. Bowtie2 has a nice [manual](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml) that explains how to use this software. 

Note that there are a variety of other good read mapping tools, such as [HiSat](https://ccb.jhu.edu/software/hisat2/index.shtml), [gmap/gsnap](http://research-pub.gene.com/gmap/), and [STAR](https://github.com/alexdobin/STAR).

The first step will be to create an index of our reference sequence (the boa constrictor mitochondrial genome).

In your terminal window:

Make sure you are in the right directory (our working directory):
```
pwd
cd ~/gdw_working   # if necessary
```

Now confirm that the boa constrictor mtDNA sequence file is there and in FASTA format: 
```
ls -lh    # should see: boa_mtDNA.fasta
```

Now, we'll use the bowtie2-build indexing program to create the index.  This command takes 2 arguments: 
(1) the name of the fasta file containing the sequence(s) you will index
(2) the name of the index (can be whatever you want)

```
~/Desktop/GDW_Apps/bowtie2/bowtie2-build boa_mtDNA.fasta boa_mtDNA_bt_index 
```

Confirm that you built the index.  You should see a bunch of files named ending in bt2, like boa_mtDNA_bt_index.3.bt2
```
ls -lh
```

Note that this index building went very fast for a small genome like the boa mtDNA, but can take much longer (hours) for Gb-sized genomes.


### Mapping reads in the SRA dataset to the boa constrictor mitochondrial genome 

Now that we've created the index, we can map reads to the boa mtDNA.  We'll map our Trimmomatic-trimmed paired reads to this sequence, as follows:

```
~/Desktop/GDW_Apps/bowtie2/bowtie2 -x boa_mtDNA_bt_index \
   -q -1 SRR1984309_1_trimmed.fastq  -2 SRR1984309_2_trimmed.fastq \
   --no-unal --threads 4 -S SRR1984309_mapped_to_boa_mtDNA.sam
```

Let's deconstruct this command line: 
```
~/Desktop/GDW_Apps/bowtie2/bowtie2    # name of the command
	-x boa_mtDNA_bt_index			   # -x: name of index you created with bowtie2-build
   -q 									   # -q: the reads are in FASTQ format
	-1 SRR1984309_1_trimmed.fastq    # name of the paired-read FASTQ file 1
	-2 SRR1984309_2_trimmed.fastq    # name of the paired-read FASTQ file 2
	--no-unal 								# don't output unmapped reads to the SAM output file (will make it _much_ smaller
	--threads 4 							# since our computers have multiple processers, run on 4 processors to go faster
	-S SRR1984309_mapped_to_boa_mtDNA.sam		# name of output file in SAM format
```

The output file SRR1984309_mapped_to_boa_mtDNA.sam is in [SAM format](https://en.wikipedia.org/wiki/SAM_(file_format)).  This is a plain text format, so you can look at the first 20 lines by running this command:


```
head -20 SRR1984309_mapped_to_boa_mtDNA.sam		
```

You can see that there are several header lines beginning with `@`, and then one line for each mapped read.  See [here](http://genome.sph.umich.edu/wiki/SAM) or [here](https://samtools.github.io/hts-specs/SAMv1.pdf) for more information about interpreting SAM files.



### Visualizing aligned (mapped) reads in Geneious

Geneious provides a nice graphical interface for visualizing the aligned reads described in your SAM file.   Other tools for visualizing this kind of data include [IGV](http://software.broadinstitute.org/software/igv/) and [Tablet](https://ics.hutton.ac.uk/tablet/)

First, you need to have your reference sequence in Geneious, preferably with annotations.  You can do this 2 ways:

1. Drag and drop the boa_mtDNA.gb file into a folder in Geneious
2. Download the file directly into Genious, using the NCBI->Nucleotide interface (search for NC_007398.1).  Once downloaded, drag from the NCBI download folder into another folder in Geneious.  

Once you have the boa constrictor mitochondrial genome in a folder in Geneious, you can drag and drop the SAM file that bowtie2 output into the same folder.  Geneious will tell you that it 'can't find the sequence it needs in the selected file'.  It is telling you it is trying to find the reference sequence to which you aligned reads.  Answer: 'Find a sequence with the same name in this Geneious folder' or 'Use one of the selected sequences' (after selecting the boa mtDNA sequence).

-A few Geneious tips:
  -Enlarge the Geneious window so that it fills the screen
  -Click View->Expand Document View to enlarge the alignment
  -Try playing with the visualization settings in the panels on the right of the alignment
 

-Some questions to consider when viewing the alignment
  -Are there any variants between this snake's mitochondrial genome sequence and the boa constrictor reference sequence?  Is that expected?
  -Can you distinguish true variants from sequencing errors?
  -What is the average coverage across the mitochondrial genome?
  -This is essentially RNA-Seq data.  Are the mitochondrial genes expressed evenly?  How does this relate to coverage?
  -Is it possible that reads that derive from other parts of the boa constrictor genome are mapping here?  How would you prevent that?
  -Can you identify mapped read pairs?  


### De-novo assembly of non-mapping reads

OK, now we've practiced mapping to a reference sequence.  Imagine instead that we don't have a reference sequence.  In that case, we'd need to perform de novo assembly.  

There are a variety of de novo assemblers with different strengths and weaknesses.  We're going to use the [SPAdes assembler](http://cab.spbu.ru/software/spades/) to assemble the reads in our dataset that don't map to the boa constrictor genome. First, let's map the reads in our dataset to the _entire_ boa constrictor genome, not just the mitochondrial genome.

The instructors have already downloaded an assembly of the boa constrictor genome from [here](http://gigadb.org/dataset/100060) and made a bowtie2 index, which can be found on your HDDs.  We could have you make an index yourself, but that would take a long time for a Gb genome like the boa constrictor's.  The boa constrictor genome index is named boa_constrictor_bt_index.

First, let's transfer the bowtie index from the HDD to your working folder:
```
#TODO: what is the real HDD path?
cp /Volumes/HDD/boa_constrictor_bt_index* ~/gdw_working
```

Now, we'll run bowtie2 to map reads to the entire boa genome.  This time we'll run bowtie2 a little differently:
1. We'll run bowtie2 in [local mode](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml#end-to-end-alignment-versus-local-alignment), which is a more permissive mapping mode that doesn't require the ends of the reads to map
2. We'll keep track of which reads _didn't_ map to the genome using the --un-conc option

```
~/Desktop/GDW_Apps/bowtie2/bowtie2 -x boa_constrictor_bt_index --local \
   -q -1 SRR1984309_1_trimmed.fastq  -2 SRR1984309_2_trimmed.fastq \
   --no-unal --threads 4 -S SRR1984309_mapped_to_boa_genome.sam --un-conc SRR1984309_not_boa_mapped.fastq
```

You should see that 90% of the reads aligned to the boa constrictor genome sequence, leaving 10% in the files that contain the non-mapping reads: SRR1984309_not_boa_mapped.1.fastq and ....2.fastq

How many non-mapping reads remain in these files?

We will use these non-mapping reads as input to our de novo SPAdes assembly.  Run SPAdes as follows:

```
~/Desktop/GDW_Apps/SPAdes/bin/spades.py   -o SRR1984309_spades_assembly \
	--pe1-1 SRR1984309_not_boa_mapped.1.fastq \
	--pe1-2 SRR1984309_not_boa_mapped.2.fastq \
	-m 12 -t 4
```

Command line options explained:
```
~/Desktop/GDW_Apps/SPAdes/bin/spades.py   
	-o SRR1984309_spades_assembly \   		# name of directory (folder) where SPAdes output will go
	--pe1-1 SRR1984309_not_boa_mapped.1.fastq \	# name of read1 input file
	--pe1-2 SRR1984309_not_boa_mapped.2.fastq \	# name of read2 input file
	-m 12 -t 4   					# use 12 Gb of RAM and 4 cores 
```

SPAdes will output a bunch of status messages to the screen as it runs the assembly.  





