## Whole Genome Alignment Tutorial
Finally, a break from the command line!
There are two parts to this tutorial
- First, we will use Geneious to both view and perform whole genome alignments in several microorganisms
- Second, an example of using [COGE](www.genomevolution.org/coge) for various comparative genomics analyses.

# Part 1:  WGA in Geneious
Here, we are going to download, align, and explore mitochondrial genomes from various felids.
- open Geneious
- Select File -> New -> Folder and let's call it "WGA_PRACTICE"
- Select the NCBI Genome database on the left
- Search for Felidae[orgn]
- Drag and drop only the mitochondrial genomes into the folder "WGA_PRACTICE"
- Select the mitochondrial genomes in our folder, and click the "Download" button
- Once downloaded, take some time to explore the data.
  - How long are the sequences?
  - Is the gene structure similar? Different?

Next, are are going to install some plugins.  Geneious has a extensive resource of plugins avaialble, oftentimes very commonly used command line programs.  Any Geneious user can actually build there own plug-in and submit it too, if needed.  We are gonig to download and install some WGA alignment plugins.
- Select Tools -> Plugins -> LastZ then click install
- Repeat with the Mauve Genome Aligner
  - Take a minute and browse the other plugins. Anything of interest for your own research?
- Go ahead and close the Plugin window after installation

Now let's align two mitogenomes using the LastZ pairwise aligner
- Select the two tiger (*Panthera tigris*) mitogenomes
- Go to Tools -> Align/Assemble -> Align Whole Genomes
- Selec the LastZ tab
  - Using the default parameters, select OK to align
- Explore the output.
- The primary visual result is a dotplot. What do you conclude?
  - A link to the description of dotplots can be found [here](https://en.wikipedia.org/wiki/Dot_plot_(bioinformatics)) or [here](https://assets.geneious.com/manual/10.1/GeneiousManualse38.html)
- Repeat the above but with two others of your choice.  Anything surprising?

Multiple Genome Alignment
- Select all 5 mitogenomes
- Go to Tools -> Align/Assemble -> Align Whole Genomes
- Selec the Mauve tab
  - Using the default parameters, select OK to align
- Explore the output
- What do the colors represent?  What does the red line graph represent?
- Which parts of genome are more poorly aligned?  Why?

So, it turns out that these felid mitogenomes are not that exciting (sorry Jill).  Let's try and align some other genomes.  
We will build on the example from yesterday of the two Batrachochytrium fungal genomes.
- Following the steps above, download the JAM81 and JEL423 Batrachochytrium dendrobatidis genomes.  Hint: (Batrachochytrium[orgn])
  - How many contigs are there?  Scaffolds? Explore the dataset using the Geneious tools.
  - How big are the genomes compared with the mitogenomes from the previous work?
- Align the two scaffold genomes using LastZ as described above.  Much slower, huh?
  - While waiting, feel free to explore the previous datasets more, or start the COGE example below.
- Once finished, look at the dot plots for the various alignments.  There is one alignment for each scaffold in the genome used as the "reference".  
  - What do you notice about alignments in long scaffolds versus short scaffolds?
  - What does the overall % simialrity look like?  Are these two genomes very similar?
- If you are feeling lucky, repeat the alignment using Mauve.... it might finish before the end of this session.  Otherwise, let's move on to one more alignment.

This last section of the WGA Geneious tutorial will align multiple *Yersinia pestis* genomes.
- Create a new folder inside the "WGA_PRACTICE" folder: Select File -> New -> Folder and let's call it "YERSINIA"
- Open, or drag and drop, the file "Yersina pestis complete genomes.geneious" from the course datasets on the hard drive into your folder
- There are many genomes, and even though bacterial, will take too long to align all of them.
- Select your favorite three *Y. pestis* genomes and align them using Mauve as described above.  Should take ~10 minutes.
- The main things to consider are:
  - Do you see any rearrangements?  Compare with your neighbor, since not everyone aligned the same three genomes.
  - How conserved are these genomes?
  - Are there any regions that have unusally high conservation or synteny? low?  Which genes are in these regions?
- Finally, think about what are the differences between alignments using LASTZ vs. Mauve.  Which would you use for your data?  Why?

# Part 2:  WGA in [COGE](www.genomevolution.org/coge)








