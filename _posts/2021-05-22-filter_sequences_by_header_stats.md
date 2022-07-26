---
layout: post
title: Filter sequences by stats from fasta headers
tags: fasta biopython
---

One of the requests I have come across is to filter sequences based on stats in headers. Example is as follows:

```
>NODE_143195_length_10_cov_16000.000000
TTGTGTTGGTTGTTGTGTTGCCTGTCTTGGTGGCGGTTGTGTTGGCTGCTTTCGTGTCAGTCTCTTCACCGATGTTATGTTGCTCTGTTGTGGCTCCGGC
>NODE_143196_length_100_cov_15891.000000
CTTGTGTTGGTTGTTGTGTTGCCTGTCTTGGTGGCGGTTGTGTTGGCTGCTTTCGTGTCAGTCTCTTCACCGATGTTATGTTGCTCTGTTGTGGCTCCGG
>NODE_143197_length_100_cov_15.000000
GCTTGTGTTGGTTGTTGTGTTGCCTGTCTTGGTGGCGGTTGTGTTGGCTGCTTTCGTGTCAGTCTCTTCACCGATGTTATGTTGCTCTGTTGTGGCTCCG
```
Now user wants to filter sequences with more than length 10 and coverage more than 15. Here is the python code

```python

from Bio import SeqIO
import sys

input=sys.argv[1]
output=sys.argv[2]
out=[]
for i in SeqIO.parse(input,"fasta"):
     isplit=i.id.rsplit("_")
     if (int(isplit[3])>20 and int(float(isplit[5]))>100) :
         out.append(i)

SeqIO.write(out, output, "fasta")

```
output from the file

```bash
 $ ./parse_fasta_header.py file.fa out.fa

$ cat out.fa 
>NODE_143196_length_100_cov_15891.000000
CTTGTGTTGGTTGTTGTGTTGCCTGTCTTGGTGGCGGTTGTGTTGGCTGCTTTCGTGTCA
GTCTCTTCACCGATGTTATGTTGCTCTGTTGTGGCTCCGG
```

