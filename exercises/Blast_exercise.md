# Command line BLAST
In this exercise, we will learn how to use the command line blast tool, also known as BLAST+.  We will use these tools to:
- blast search NCBI databases remotely
- build our own blast database
- blast locally
- retrieve information from blast hits

The BLAST+ suite comes with the following tools.  We won't use all of them, but feel free to read about them [here](https://www.ncbi.nlm.nih.gov/books/NBK279690/):  You should recognize some of them from the lecture earlier.
- blast_formatter
- blastn
- deltablast
- makembindex
- rpstblastn
- update_blastdb.pl
- blastdb_aliastool
- blastp
- dustmasker
- makeprofiledb
- segmasker
- windowmasker
- blastdbcheck
- blastx
- legacy_blast.pl
- psiblast
- tblastn
- blastdbcmd
- convert2blastmask
- makeblastdb
- rpsblast
- tblastx


## Part 1:  Remote BLAST
When we blast "remotely", we are using our command line to submit a blast search to the NCBI server.
This means we must have an internet connection in order to perform the search.

**Note**: The time to complete a remote blast will vary depending on the sequence length, complexity, number of sequences, database, and internet speed.
For this first example, let's download an example protein sequence:
Wild camel (*Camelus ferus*) ferritin light chain protein [XP_014416718.1](https://www.ncbi.nlm.nih.gov/protein/XP_014416718.1).

Select -> Send To -> File  
This should be saved in the downloads folder as "sequence.fasta"  
Now let's BLAST!!!
```
# Make a new folder move into it
mkdir BLAST_PRACTICE
cd BLAST_PRACTICE

# Lets move the camel sequence file here and rename it to something more useful
mv ~/Downloads/sequence.fasta camel_ferritin.faa

# Open the contents of the file
cat camel_ferritin.faa

# To get the help menu for any of our blast tools, type:
blastp -help

# Now Blast to the refseq-protein database
blastp \
   -query camel_ferritin.faa \
   -db refseq_protein \
   -remote \
   -out camel_ferritin.blastout
```
So this search should take a couple minutes at most.  Open the contents of the file and explore:
```
# Open the file to screen
cat camel_ferritin.blastout

# More convenient way of opening large files:
less -S camel_ferritin.blastout
```
The results look good. However, this output format can be difficult to parse if we have thousands and thousands of sequences.
Let's repeat the search again, but this time with some changes.
```
# New Blast Search
blastp \
   -query camel_ferritin.faa \
   -db refseq_protein \
   -remote \
   -num_alignments 10 \
   -outfmt 6 \
   -out camel_ferritin.blastout.tsv
```
Open the contents of the new output file.
What is different?  Do you think this would be easier or more difficult than the previous output to calculate various statistics, make plots, etc?

## Part 2:  Building a database and local BLAST
The remote blast above is convenient, but when no internet connection is available, or when there are thousands of sequences, this may not be optimal.  In these cases and many others, it is easiest to build your own BLAST database and perform the search on your own computer.

In this section, we are going to download the transcriptome (remember what a 'transcriptome' is?) from the pathogen *Trichinella patagoniensis*.  [Krivokapich et al, 2012 doi:10.1016/j.ijpara.2012.07.009](http://www.sciencedirect.com/science/article/pii/S0020751912001932). This genus of nematode worms causes the disease trichinellosis, and infect and/or are transmitted between a variety of mammals, including humans. This particular species was described from South American pumas. We will download the transcriptome from NCBI's Transcriptome Shotgun Assembly ([TSA](https://www.ncbi.nlm.nih.gov/genbank/tsa/)) database.
From the above link:
- Search for accession GECA00000000.1
- Click on contig link at bottom (To the right of "TSA")
- Go to download tab
- Clck and download fasta link

Or download from the command line using the command below:
```
curl -O ftp://ftp.ncbi.nlm.nih.gov/sra/wgs_aux/GE/CA/GECA01/GECA01.1.fsa_nt.gz
```

Now let's process the data and build a blastable database
```
# Uncompress the file.  What format is it?
gunzip GECA01.1.fsa_nt.gz

# How many sequences are there?
grep -c "^>" GECA01.1.fsa_nt

# Get help menu for building a database
makeblastdb -help

# Build the database
makeblastdb \
   -in GECA01.1.fsa_nt \
   -input_type fasta \
   -dbtype nucl \
   -title Tpat \
   -parse_seqids \
   -out Tpat
```


