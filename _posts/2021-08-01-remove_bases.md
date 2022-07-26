---
layout: post
title: "Remove bases in between the sequences"
tags: fasta seqkit awk parallel
---

Some times a biologist needs to chuck few bases out from middle of the sequences and stich remaining parts. Here are the input files:  a fasta file and a text file with sequence names and coordinates of regions to be chucked out from each sequence. Both fasta file and text file have sequence names to match.

```bash

$ cat test.fa 
>seq1
atgcgcgctgc
>seq2
cgttgcgtatgcgcg

$ cat test.txt 
seq1	3	5
seq2	4	6

```

In test.txt, 2nd and 3rd columns are start and end coordinates of regions to be left out from each matching sequence from sequences mentioned in column1. Let us do it. Code and output as follows:

```bash

$ parallel --col-sep "\t" "seqkit -w 0 grep -p {1} test.fa | awk '/>/;\!/>/ {print substr(\$0,1,{2}-1)substr(\$0,{3},length(\$0)-{3}+1)}'"  :::: test.txt 

>seq1
atgcgctgc
>seq2
cgtcgtatgcgcg

```
