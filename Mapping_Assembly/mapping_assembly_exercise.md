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
bowtie2-build boa_mtDNA.fasta boa_mtDNA_bt_index 
```

Confirm that you built the index.  You should see a bunch of files named ending in bt2, like boa_mtDNA_bt_index.3.bt2
```
ls -lh
```

Note that this index building went very fast for a small genome like the boa mtDNA, but can take much longer (hours) for Gb-sized genomes.



#### Mapping reads in our dataset to the bowtie index

Now that we've created the index, we can map reads to it.  We'll map our Trimmomatic-trimmed paired reads to this sequence, as follows:

```
bowtie2 -x boa_mtDNA_bt_index -q -1 SRR1984309_1_trimmed.fastq  -2 SRR1984309_2_trimmed.fastq --no-unal --threads 4 -S SRR1984309_mapped_to_boa_mtDNA.sam
```

Let's deconstruct this command line: 
```
bowtie2 
	-x boa_mtDNA_bt_index				# -x: name of index you created with bowtie2-build
   -q 										# -q: the reads are in FASTQ format
	-1 SRR1984309_1_trimmed.fastq  				# name of the paired-read FASTQ file 1
	-2 SRR1984309_2_trimmed.fastq 				# name of the paired-read FASTQ file 2
	--no-unal 											# don't output unmapped reads to the SAM output file (will make it _much_ smaller
	--threads 4 										# since our computers have multiple processers, run on 4 processors to go faster
	-S SRR1984309_mapped_to_boa_mtDNA.sam		# name of output file in SAM format
```

The output file SRR1984309_mapped_to_boa_mtDNA.sam is in [SAM format](https://en.wikipedia.org/wiki/SAM_(file_format)).  This is a plain text format, so you can look at the first 20 lines by running this command:


```
head -20 SRR1984309_mapped_to_boa_mtDNA.sam		
```

You can see that there are some header lines beginning with `@`, and then one line for each mapped read.



#### De-novo assembly of non-mapping reads






