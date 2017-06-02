# Command line BLAST
In this exercise, we will learn how to use the command line blast tool, also known as BLAST+.  We will use these tools to:
- blast search NCBI databases remotely
- build our own blast database
- blast locally
- retrieve information from blast hits

The BLAST+ suite comes with the following tools.  We won't use all of them, but feel free to read about them [here](https://www.ncbi.nlm.nih.gov/books/NBK279690/):
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
