---
layout: post
title: Trim fasta sequences based on a sequence in fasta file
tags: fasta seqkit
---
I have come across this request where user wants to trim a multi sequence fasta file as per the very first sequence in an aligned file with gaps. Following is the example

```bash

$cat test.fa

>seq1
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
-------------------------------cttgagctggggtctggccatggggtaaa
gaagcagcagcagagacagaccaatgccaatg----------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
-----------
>seq2
ggatcagcaccagccccggctgctccttcaccttgagttgtggccgggccatggggtaga
gcaggagcaacaaggacagcccgataccgatcaggatcccatactcaatgcccacacata
gtgaacccaaaaaggtggccacatgcacaaacagatcccatttattggtgcgccacaaaa
cggggatgattttgtagtcgaccatctgcatgacggccatgatgatgaccgcggccagcg
ctgacttggggatgtagtaacagtagggcaccaggaaggccagtactaacaggatcaggg
accctgtgaaaagaccattcgccggtgttcttacaccgctctgtgagttgacagcagttc
tggaaaaactgccggtgacaggataggaatgaacaaaggaactgagaatgttggcagtac

```
As you see, user has an aligned file. Very first sequence has the sequence of interest, but has leading and lagging gaps. Now user wants to extract the first sequence and the sequence at those coordinates for rest of the sequences, in alignment file. Following is the soltuion:

First let us the get the coordinates of the sequence:

```code

$seqkit head -n 1 test.fa | seqkit locate -Prip '([ATGC]+)' 

seqID	patternName	pattern	strand	start	end	matched
seq1	([ATGC]+)	([ATGC]+)	+	212	272	cttgagctggggtctggccatggggtaaagaagcagcagcagagacagaccaatgccaatg

```

Now let us take the coordinates 212 and 272, and extract the sequence between those positions for all the sequences:

```code
$ seqkit -w 0 subseq -r 212:272 test.fa                      

>seq1
cttgagctggggtctggccatggggtaaagaagcagcagcagagacagaccaatgccaatg
>seq2
gacggccatgatgatgaccgcggccagcgctgacttggggatgtagtaacagtagggcacc

```

Interesting request I would say.
