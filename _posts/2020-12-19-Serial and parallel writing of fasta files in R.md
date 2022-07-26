---
title: "Serial and Parallel writing of Individual fasta files in R"
tags: [serial 'Fasta' parallel 'DNAstrings']
layout: post
---
Biological sequence analysts do lots of I/O on sequence files (nt,aa and others). One of the standards formats is fasta. Fasta file can hold multiple or single sequence. One of the requirements to write each sequence in multiple sequence fasta  file to an individual file and the file name must correspond to the name of the sequence. Following is input example:

```bash
$ cat test.fa 
>gene1
ACATATTGGAGGCCGAAACAATGAGGCGTGATCAACTCAGTATATCAC
>gene2
CTAACCTCTCCCAGTGTGGAACCTCTATCTCATGAGAAAGCTGGGATGAG
>gene3
ATTTCCTCCTGCTGCCCGGGAGGTAACACCCTGGACCCCTGGAGTCTGCA

```
Now the requirement is to extract each sequence into an individual file and the file name correspond to sequence name (here: gene1.fasta, gene2.fasta, gene3.fasta)

Let us do it in R. 

```R
# Load file
library(Biostrings)
genes=readDNAStringSet("test.fa", format = "fasta")
```

Now let us write each sequence into a file in a loop.

```R
for (i in names(genes)){
    writeXStringSet(genes[i],paste0(i,".fasta"))
}
```
Now let us do the same in parallel using sapply function
```R
sapply(names(genes), function(x) writeXStringSet(genes[x], paste0(x,".fasta")))
```