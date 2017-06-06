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

We will be using the 'terminal' on the mac.  The terminal is how we interact with the computer without a user interface. Everyone here has various degrees of experience with the command line, so, especially in the beginning, I will try and explain some basics as we progress.  Please don't hesitate to ask if you have any questions regarding commands, parameters, etc.
Here are some basic commands we will use for reference.  We will learn more along the way:
```
# How to get the manual or help menu for a command/program ("grep" example)
man grep
grep -h

# change into a folder
cd Folder

# List the contents of a folder
ls
ls -lh
ls -lh Folder

# Open a file to the screen
cat file

# Open large files page by page, without wrapping lines
less -S file
   # type "q" to quit
   
# Write result of command to a new file
cat file > newfile

# Look at the first 10 lines of a file
head -10 file

# Last 10 lines
tail -10 file

# Search for lines matching "abc" in a file
grep "abc" file

# Select a column from a delimited ("tab" by default)
cut -f3 file
```

## Part 1:  Remote BLAST
When we blast "remotely", we are using our command line to submit a blast search to the NCBI server.
This means we must have an internet connection in order to perform the search.

**Note**: The time to complete a remote blast will vary depending on the sequence length, complexity, number of sequences, database, and internet speed.
For this first example, let's download an example protein sequence:
Wild camel (*Camelus ferus*) ferritin light chain protein [XP_014416718.1](https://www.ncbi.nlm.nih.gov/protein/XP_014416718.1).

Select -> Send To -> File -> Format: fasta  
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

# Repeat the above search, but limit it to a specific species
blastp \
   -query camel_ferritin.faa \
   -db refseq_protein \
   -remote \
   -out alpaca_ferritins.blastout \
   -entrez_query "Alpaca[ORGN]"

```
The \ at the end of the line tells the computer that the command will continue onto the next line. This notation can help make really long commands look cleaner and easier to understand.  
This search should take a couple minutes at most.  Feel free to try and modify the above command to search your favorite taxonomic group. Open the contents of the files and explore:
```
# Open the file to screen
cat alpaca_ferritins.blastout

# More convenient way of opening large files:
less -S alpaca_ferritins.blastout
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
What do the output files look like?  Can you open them?

Let's select some random sequences from the transcriptome to use as a query.  We will use the [seqtk](https://github.com/lh3/seqtk) toolkit from Heng Li. This toolkit is fast and a standard for basic processing of sequence files (fasta and fastq).
```
# Select 5 sequences at random
~/Desktop/GDW_Apps/seqtk-master/seqtk sample GECA01.1.fsa_nt 5 > sample5.fasta

# Remember how to check the number of sequences?
grep -c "^>" sample5.fasta
```
Next, we are going to blast these sequences against the transcriptome database we made. What do you expect to find?
```
blastn \
   -query sample5.fasta \
   -db Tpat \
   -out sample5.blastout \
   -num_alignments 10 \
   -outfmt 6 \
   -num_threads 2
```
How many hits are there in total?
Let's repeat but filter for only the extremely small evalues.
Do you expect more matches or fewer matches?
```
blastn \
   -query sample5.fasta \
   -db Tpat \
   -out sample5.blastout2 \
   -num_alignments 10 \
   -outfmt 6 \
   -num_threads 2 \
   -evalue 1e-50
```
For the last part, we are going to retrieve the sequences for our matches from the above BLAST search.
To do this, we need to make a list of the accession numbers of our matches (This only works if we built the database using the 'parse_seqids' parameter), and then retrieve the matching sequences.
```
# Grab the second column from the blast results
cut -f2 sample5.blastout2 > matches.list

# Retrieve the sequences for these matches
blastdbcmd \
   -db ./Tpat \
   -entry_batch matches.list \
   -out matches.fasta

# Interested in other formats or options?
blastdbcmd -help
```

## Part 3: Extra credit
[Wu et al. 2008 (doi:10.1016/j.parint.2008.03.005)](http://www.sciencedirect.com/science/article/pii/S1383576908000391) identified a set of candidate genes that were differentially expressed between various *Trichinella* infections.  One of these genes was the pax7 protein, accession: [KRY39300.1](https://www.ncbi.nlm.nih.gov/protein/954373245?report=fasta).
Is this gene expressed in the *Trichinella patagoniensis* transcriptome?
If so, are there multiple paralogs?  
Hint: you will be searching a nucleotide database with a protein sequence.




