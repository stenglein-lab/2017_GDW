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
Wild camel (Camelus ferus) ferritin light chain protein [XP_014416718.1](https://www.ncbi.nlm.nih.gov/protein/XP_014416718.1)
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
   -out test2
```



