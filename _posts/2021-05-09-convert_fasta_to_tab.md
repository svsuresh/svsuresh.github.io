---
layout: post
title: "Convert fasta files to tab separated file"
tags: awk seqkit paste shell 
---

In earlier post, I posted tips to convert tab (tsv). In this note, let us convert the fasta to tab. Please note that for all the shell programs (AWK, sed, bash), sequences must be in single line, where as seqkit can handle multiline fasta

Here is an example fasta for testing the code
```code
>seq1
ATgc
>seq2
gccat
>seq3
tagc
```
Here is the code to convert fasta to tab
```bash
$ seqkit fx2tab test.fa
$ awk -v OFS="\t" -v RS=">" 'NR > 1 {print $1, $2}' test.fa 
$ paste - - < test.fa | sed 's/^>//'
```
