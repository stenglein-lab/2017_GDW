t # In this exercise, we will de novo assemble a viral genome from a publically available dataset and will use read mapping to validate our assembly.


## Download SRA dataseto

We will 

```
cd
mkdir boa_sra
cd boa_sra
```

The dataset we will use b

```
curl -O ftp.ncbi.nlm.nih.gov:/sra/sra-instant/reads/ByRun/sra/SRR/SRR198/SRR1984309/SRR1984309.sra
```

5 sec over wifi

(*) Convert SRA->FASTQ

```
fastq-dump --split-files SRR1984309.sra

mv SRR1984309_1.fastq SRR1984309_R1.fastq
mv SRR1984309_2.fastq SRR1984309_R2.fastq
```

~1 sec



(*) Download boa constrictor (mtDNA) genome.

Goto NCBI Taxonomy database: https://www.ncbi.nlm.nih.gov/taxonomy
Search for "boa constrictor"
Click on boa constrictor link
Click on genome (1) link

Click on 'See also 1 organelle- and plasmid-only records matching your search/
Click on 'NC_007398.1' RefSeq link

Option 1:
download sequence from NCBI website
Send->complete record->file->format[genbank or fasta]

Option 2:
download in Geneious.  Copy accession #.  Goto NCBI->Nucleotide in Geneious.  Search for accession.  Create a new folder.  Drag sequence to new folder.

# Create

Create 



