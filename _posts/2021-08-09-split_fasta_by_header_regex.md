---
layout: post
title: "Split fasta file into multiple files based on partial headers"
tags: fasta awk seqkit
---

There are times where one needs to split fasta file based on header information. However, let us say we need to split the fasta file based on regex, it needs either special tools like `seqkit` or some creating thinking in `awk`. Let us do this in awk and then in seqkit. Let us take an example of a fasta file with sequences in single line.

```bash
$ cat test.fa 

>seq1_test
atgc
>seq2_test
acgat
>seq3_test
cgtc
>seq1_test
cgatgc

```

Requirements to split this fasta file into 3 fasta files based on first 3 letters of the headers (seq1,seq2, seq3). Let us do this in awk:

```bash

$awk -F '[>_]' '/>/{getline seq; print $0"\n"seq > $2".fa"}' test.fa

```

Now you should see three files in current folder wherever test.fa is located. One of the short comings of this program it doesn't work on multiline sequences. Ofcourse, you can use another awk command to flatten sequence (from multiline to single line). Instead of that, we can use `seqkit`. Let us a take a multiline fasta file and use seqkit to split into multiple fastas:

```bash

$cat test.fa 
>seq1_test
atgc
>seq2_test
acgat
cgt
>seq3_test
cgtc
>seq1_test
cgatgc
catc
ct

```

Let us use seqkit to do the job:

```bash

$seqkit -w 0 split -i --id-regexp '(^[A-Z]{3})_*' test.fa -O new_files

```

This would created 3 files in a folder named `new_files`. I prefer seqkit over awk for this kind of operations as it supports regex separately two components of fasta format (header, sequence)