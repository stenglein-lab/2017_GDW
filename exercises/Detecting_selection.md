# Detecting Selection
In this exercise we are going to look for evidence of selection by searching for regions of very low genetic variation.  We will do this by searching the alignment of xxxx genomes using a sliding window.
A sliding window means we are going to:
- start from the beginning of the alignment
- calculate a parameter for a small region (e.g., 100 bases)
- move the window a set number of bases (e.g., 50 bases)
- calculate the parameter again for the new window.
- repeat until we slide the window across the entire alignment.
- Finally, we can summarize our parameters using basic statistics like histograms, means, standard deviations in your favorite stats program (e.g., R, Excel)

A visual example of a sliding window is available below:
![window plot](./windows.png)


Let's begin.

## Step 1: Preparing the software
We are going to use a quick and easy tool, called "gd", written to calculate various measures of genetic variation (i.e., pi, S [# segregating sites], and Tajima's D) in sliding windows across the alignments.  However, this program is not available yet on your computers.  We will have to download it and install it. It is a relatively simple installation, so I pray it works (my figners are crossed!!!).
```
# Let's move to our home folder
cd ~

# Make a new folder for this exercise
mkdir WINDOW_ANALYSIS

# Move into this new folder
cd WINDOW_ANALYSIS
```

Awesome!  Now we are in our new folder, and ready to download the program "gd".  You can find the program [here](http://guanine.evolbio.mpg.de/bioBox/).  There are two ways to download this program.  You can click on the link to "gd, v0.12".  The file should automatically be downloaded to your "Downloads" folder.  The other method is to download the file using the "curl" command we learned earlier in the week.

If you used the first method (clicking on the download link) then follow these steps
```
# Move the gd program file to your current location
  # The "." means put the file in your current folder
mv ~/Downloads/gd_0.12.tgz .
```

If you want to download using the command line then do this:
```
# Download directly using the curl command.  I copied the link from the website by control-clicking (right-clicking) and selecting "copy link address"
curl -O http://guanine.evolbio.mpg.de/bioBox/gd_0.12.tgz
```

Good! No matter how you downloaded the file, you should now have a file called gd_0.12.tgz in your folder.  Remember how to check the contents of your folder (Hint: ls)?
This file is actually a compressed folder.  We have to uncompress and unpack this file to access its contents.  We can do it in one step.
```
# Unpack and uncompress this file.
tar -zxvf gd_0.12.tgz
```

There should not be a new folder called "Gd_0.12".  Check with your "ls" command.
Now, we are going to move into this folder, then "compile" the program.  It is common for various bioinformatics programs to be sent in this form.  "Compiling" just means to assemble the program from various pieces and customize it to your particular computers settings.  It is "usually" super easy.
Here we go...
```
# Move into the Gd program folder
cd Gd_0.12

# Compile it (it may take a few minutes)
make

# Test that the program works (and read the help menu)
./gd -h

# Move back into your previous folder (the ".." means 'up one folder')
cd ..
```






