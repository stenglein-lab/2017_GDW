## A (partial) glossary of genomics and NGS terminology

GDW 2017
Mark Stenglein

### 16S

The [16S](https://en.wikipedia.org/wiki/16S_ribosomal_RNA) ribosomal RNA gene is present in all bacterial and archaeal genomes.  This gene is sufficiently conserved that primers that anneal to conserved regions of the gene will amplify essentially any prokaryotic 16S rRNA gene.  These PCR products (amplicons) can be sequenced to provide a survey of microbial diversity in a sample.

### Adapter

Most NGS instruments require that adapters of known sequence be added to the 2 ends of dsDNA molecules that will be sequenced on the instrument.  Adapters are added in a variety of ways to starting nucleic acid molecules during library preparation.  Adapters serve multiple purposes. For instance in Illumina sequencing, adapters allow molecules to anneal to flow cells and be amplified into a clonal [cluster](#cluster). Adapters often contain index sequences (barcodes) that allow samples to be multiplexed.  

### Amplicon sequencing

A type of NGS in which PCR products (amplicons) are sequenced.  16S sequencing is one type of amplicon sequencing.  This is in contrast to shotgun sequencing, where libraries consist of complex populations of materials that derive randomly from the starting nucleic acids.

### Assemble/Assembly

NGS typically produces reads that are shorter than the nucleic acids from which they derive.  Assembly is the process by which these short reads are stiched together to attempt to recreate the startng nucleic acid sequence.  

### Cluster

In Illumina sequencing, library molecules are hybridized to a flow cell and amplified into a luster of a few thousand clonal copies.  The large number of copies in a cluster boosts the signal from the incorporation of fluorescent nucleotides, but when the clonal copies in a cluster get out of sync, phasing issues arise, which increases error rates as sequencing progresses.

Consensus sequence
Contig
Coverage

### Cycle

In Illumina sequencing, chain-terminated nucleotides are incorporated to cause the newly synthesized strand to extend one base at a time.

### De novo assembly

De novo assembly refers to the fact that most of the time, [assembly](#assembly) involves the reconstruction of an unknown sequence.  Most assemblies are de novo assemblies.   

Deep sequencing
FASTA
FASTQ
Indel
Library
Library Prep
Mapping
N50

### NGS

[Next generation sequencing](https://en.wikipedia.org/wiki/DNA_sequencing#High-throughput_methods), aka high throughput sequencing, aka 'deep' sequencing.  Any of a number of sequencing technologies that enable the simultaneous sequencing of a large number of molecules.

Read
Reference sequence
SBS
SMS
SNP
Scaffold
Sequence
Structural variation
Variant
WGS
Word
barcode
de novo assembly
exome
genes
index
kmer
long read
metagenomics
multiplexing
noncoding
paired end
short read
single end
transcriptome
variants

