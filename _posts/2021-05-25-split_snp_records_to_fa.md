---
layout: post
title: Split each sequence with two alleles into two sequences and convert the text to fasta
tags: snp snv fasta awk split
---

One of the requests I came across is to split the sequence with two alleles into two sequences. Each one carrying one allele each and then convert the all sequences to fasta records. Eg is given below

```bash

input sequence: AT[G/C]CT

```

Expected output:

```bash
>seq1
ATGCT
>seq2
ATCCT

```

A few records below with the code. First input:

```bash
AAGGGTTTAGAAAAAAACCAAACAAACAATCGAAA[C/T]GAAATAGAAAAAGAAAAAGGGAAGGGGTTAAGTTC
TTCATATAAAAATTGATATAGAATCTTTGAAAAAG[A/C]CCTTTCTTCCTAAGAAAGAAAAGGCTTACTGTCTT
CCCAAATAAACAGGTATGGAAGCTATAATTGGAAA[C/T]CACGATCGAATTTATGGAAGCATTGGTTTATACAT
```

Code with output:

```bash

$awk -F "[][/]" -v OFS="\n" '{print $1$2$4,$1$3$4}' test.txt | awk  -v OFS="\n" '{print ">seq_"NR,$0}'

>seq_1
AAGGGTTTAGAAAAAAACCAAACAAACAATCGAAACGAAATAGAAAAAGAAAAAGGGAAGGGGTTAAGTTC
>seq_2
AAGGGTTTAGAAAAAAACCAAACAAACAATCGAAATGAAATAGAAAAAGAAAAAGGGAAGGGGTTAAGTTC
>seq_3
TTCATATAAAAATTGATATAGAATCTTTGAAAAAGACCTTTCTTCCTAAGAAAGAAAAGGCTTACTGTCTT
>seq_4
TTCATATAAAAATTGATATAGAATCTTTGAAAAAGCCCTTTCTTCCTAAGAAAGAAAAGGCTTACTGTCTT
>seq_5
CCCAAATAAACAGGTATGGAAGCTATAATTGGAAACCACGATCGAATTTATGGAAGCATTGGTTTATACAT
>seq_6
CCCAAATAAACAGGTATGGAAGCTATAATTGGAAATCACGATCGAATTTATGGAAGCATTGGTTTATACAT

```

But what if you want to append the allele to the headers and retain sequence order? Following is the code:

```bash
$ awk -F "[][/]" -v OFS="\n" '{print ">seq_"NR"_"$2,$1$2$4,">seq_"NR"_"$3,$1$3$4}' test.txt 

>seq_1_C
AAGGGTTTAGAAAAAAACCAAACAAACAATCGAAACGAAATAGAAAAAGAAAAAGGGAAGGGGTTAAGTTC
>seq_1_T
AAGGGTTTAGAAAAAAACCAAACAAACAATCGAAATGAAATAGAAAAAGAAAAAGGGAAGGGGTTAAGTTC
>seq_2_A
TTCATATAAAAATTGATATAGAATCTTTGAAAAAGACCTTTCTTCCTAAGAAAGAAAAGGCTTACTGTCTT
>seq_2_C
TTCATATAAAAATTGATATAGAATCTTTGAAAAAGCCCTTTCTTCCTAAGAAAGAAAAGGCTTACTGTCTT
>seq_3_C
CCCAAATAAACAGGTATGGAAGCTATAATTGGAAACCACGATCGAATTTATGGAAGCATTGGTTTATACAT
>seq_3_T
CCCAAATAAACAGGTATGGAAGCTATAATTGGAAATCACGATCGAATTTATGGAAGCATTGGTTTATACAT

```