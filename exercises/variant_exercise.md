## Intrahost variation exercise

GDW 2017
---

### In this exercise, we will go through a variant calling pipeline. 

The data we'll use was generated from a pool of _Anopheles gambiae_ mosquitoes collected in Liberia.  The dataset contains reads mapping to a virus (Anopheles flavivirus).  We'll map the reads to the viral genome and use a variant caller to identify variants and determine their frequencies.

We will:

- download the files we need
- make a bowtie index from the viral genome (reference) sequence
- map reads to the reference sequence using bowtie2
- use lofreq to call variants
- visualize and inspect variants in vcf output file and in Geneious


### Download files needed for exercise from github

First, we need to download some files.

Open the terminal and change directory to a directory of your choosing or stay where you are.

The files for this exercise are in the GitHub repository.  You can download them using this command:

```
curl -O https://raw.githubusercontent.com/stenglein-lab/2017_GDW/master/exercises/variant_exercise_files.tar.gz
```

The file extension `.tar.gz` is similar to .zip.  It means this is a compressed set of files.  

You'll often run across `.tar.gz` files when you're downloading data or software.  You can uncompress and extract this file using the tar command, as follows:

```
tar xvzf variant_exercise_files.tar.gz
```

You should now see 5 new file (run the `ls` command):
```
Pool_filtered_R1.fastq   # paired read files containing already trimmed reads in FASTQ format
Pool_filtered_R2.fastq
viral_genome.fasta       # viral genome in FASTA format
viral_genome.gb          # viral genome in GenBank (annotated) format
lofreq                   # the variant caller we'll use
```

### Create a bowtie index from the viral genome sequence

We're going to use bowtie2 to map reads in the dataset to the viral genome.  First, we need to create a bowtie2 index.  Remember how to do that?

```
~/Desktop/GDW_Apps/bowtie2/bowtie2-build viral_genome.fasta viral_genome_bt_index 
```


### Mapping reads in the dataset to the viral genome sequence 

Now that we've created the index, we can map reads to it.  We'll use [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml) to do this, as follows

```
~/Desktop/GDW_Apps/bowtie2/bowtie2 -x viral_genome_bt_index \
   -q -1 Pool_filtered_R1.fastq  -2 Pool_filtered_R2.fastq \
   --no-unal --threads 4 -S Pool_reads_aligned_to_viral_genome.sam
```

### Using lofreq to call variants

Now we have mapped reads.  There are a variety of variant callers (see review articles cited in lecture).  We'll use the [lofreq](http://csb5.github.io/lofreq/) variant caller, because it is pretty easy to use and has performed reasonably well in benchmarking comparisons.

Most variant callers take as input sorted .bam format files, which are a compressed, sorted version of sam files.  We'll use the samtools program to convert our .sam output file from bowtie to .bam format:

```
# convert SAM -> BAM
samtools view -b Pool_reads_aligned_to_viral_genome.sam > Pool_reads_aligned_to_viral_genome.bam

# sort BAM 
samtools sort -T tmp -O 'bam' Pool_reads_aligned_to_viral_genome.bam  > Pool_reads_aligned_to_viral_genome.sorted.bam
```

Now, we'll run lofreq.  To learn more about how you could run lofreq, run:
```
./lofreq         # show general usage info
./lofreq call    # show general usage info for the call command in lofreq (actually does variant calling)
```

To call variants, you tell lofreq the name of the reference sequence (-f option), the name of the sorted bam file, and the name of the output file that will be created (-o option).  The output is in [VCF format](https://samtools.github.io/hts-specs/VCFv4.3.pdf):
```
./lofreq call -f viral_genome.fasta Pool_reads_aligned_to_viral_genome.sorted.bam -o Pool_reads_aligned_to_viral_genome.vcf
```

Use less or cat to inspect the contents of your vcf file.  
```
less Pool_reads_aligned_to_viral_genome.vcf
```
Questions to consider:
- Does the VCF file make sense?  
- How many variants were identified?
- What are their allele frequencies?
- Is linkage between variants described?
- Are the variants SNPs, or InDels?  Would you expect to see InDel variants here?

### Time permitting: Visualize and inspect mapped data supporting called variants in Geneious

First, you need to get your reference sequence into Geneious, preferably with annotations.  Then you need to bring in the mapped reads (can bring in .sam or .bam format)

1. Drag and drop the viral_genome.gb file into a folder in Geneious
2. Drag and drop the Pool_reads_aligned_to_viral_genome.sorted.bam file into the same folder in Geneious.  (Tip: select the viral_genome first, then select "Use one of the selected sequences" when it asks you to locate the reference sequence).


**Some questions to consider:**
- Can you identify variants called in your VCF file?  (*Note:* numbering differs between the consensus sequence and the reference sequence in Geneious.  #s in the VCF file correspond to reference sequence positions).
- Do variant frequencies match between Geneious and the VCF file?
- Can you identify linked variants?  How far apart can you identify linked variants?
- Are any of the variants non-synonymous?
- Are these intrahost or interhost variants?
- Can you identify any InDel variants that _should_ have been called that weren't? (Hint: look near the beginning of the reference sequence.)
- We ran bowtie2 in end-to-end mode, which doesn't permit soft-trimming of read ends.  
  - Can you identify any ends that should have been trimmed but weren't?  (I.e. that contain super crappy basecalls?)
  - Can you identify any ends that might have been inappropriately soft-trimmed (the trimming of which could have artifically decreased variant allele frequencies?)



### To download all the content from the GitHub site to your external disk drive

To have a copy of all the material to take home with you.

```
# change directory to your external hard drive
cd /Volumes/GDWDrive/     

# 'clone' the repository (download a copy)
git clone https://github.com/stenglein-lab/2017_GDW.git
```

This will create a new directory on your external drive named 2017_GDW with all the course material that's on the github repository.

If the GitHub site gets updated, you can update your local copy using the `git pull` command

```
# change directory to the GDW_2017 directory in your external hard drive
cd /Volumes/GDWDrive/2017_GDW/

# update the repository using pull command
git pull
```

You can also download the entire repository directly from the github site (as a .zip file), and our intent is to leave this site up indefinitely.


