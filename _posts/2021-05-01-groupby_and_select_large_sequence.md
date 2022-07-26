---
layout: post
title: "Select longest sequence in a fasta with identical headers"
tags: fasta awk datamash
---
One of the requests I have come across to de-duplicate fasta file. Example fasta file has multiple sequences are varying length with identical headers. I have checked several tools including datamash, tsv-utils, bedtools that do this kind of work. Most of them allow the user to select last, first, random sequence from duplicate sequences, but not the longest one. Example fasta is as follows:

```bash
>seq1
atgc
catg
>seq1
gtac
cagtgacg
>seq1
atgcgctgacgacgagcg
>seq11
atgc
>seq11
gatc
>seq3
atgcgcgatcgatcccggggcc
```
you see that there are identical headers and also sequences are not in single line. Now we have to pick one sequence per a group of identical headers and that sequence must be longest within that group. Here is the solution:

```awk

$ awk  '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}} END {print "\r"}' test.fa | awk -v OFS="\t" -v RS=">" 'NR>1 {print $1,$2, length($2)}' | datamash -sg1 max 3 -f | awk -v OFS="\n" '{print ">"$1,$2}'

>seq1
atgcgctgacgacgagcg
>seq11
gatc

```

First part of the awk code (till `test.fa`) linearizes the fasta i.e makes all sequences single lined if there are spread across multiple lines within a fasta record. Next line prints length of the sequence in addition to original record, but in tab separate values. Datamash sorts the data and groups the values on 1st column (fasta heaers here), picks up max value from 3rd column. 3rd column contains length of the sequences (from previous awk command). Now pring the columns we want and output is separated by newlines.

Same can be done with awk only in follwing way:
```awk
$ awk  '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}} END {print "\r"}' test.fa| awk -v OFS="\n" -v RS=">" ' NR>1 {if (length(a[$1]) < length($2))a[$1]=$2;}END{for(i in a){print ">"i,a[i];}}'
```
