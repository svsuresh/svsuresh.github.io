---
layout: post
title: Identify sequences with contiguous nucleotide blocks of chosen size in multiple sequence alignment
tags: awk seqkit fasta
---
One of the posts required to identify the sequences with contiguous stretch of 180 nucleotides, from a multiple sequence alignment. Below is the sequence file and output from the code. I have used awk and seqkit. Both the scripts give identical results

**input fasta**
```fasta
>Mitra_TRINITY_DN40971_c28_g1_i1
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------t--------------------------------------ctc
aattattactgctt------tccaaaatcatgctgcac------------atcaattaat
gaggtgat-------ctgtcatggaattcctgacatgagaccattagaa--agtggtgac
attatcaacattgacat-----cactgcctaccatcggaattttcatggagacttgaacg
agaccatgtttgtcggggaggtggatgaggccagcaaagagctagtgaagacgacgcatg
aatgtctgatgatggccatcgatgaagtgaaaccaggggtg-------------------
------------------------------------------------------------
------cggtacagagagatgggaaacatcatccagaaa---cacgcccagtctcatggc
tactctgttgtccgcagctactgcggtcatgggattcaccaattgttccacactgctcca
agtgtgcctcattatgggaagaataaa------------gccatcggtgtgatgaaacca
ggtcatactttcaccattgagccaatgatatctcaaggcacctggcgtgatgagatgtgg
ccagacaactggactgcagtgacacaggacggcaaacgctcagcgcagtttgagcacaca
ctgctggtgact------------------------------------------------
-------------------------gacacaggatgcgaagtcttgacccgtcgtcgggc
aaatgatggacagccccactttatggactccaaatga------------------tggac
agcca-------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
----------------------------------------------------
>Mitra_TRINITY_DN40971_c28_g2_i1
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
------------------------------------------------------------
---------------------------------------------------------gtg
ctcttccgatctgccaaagaaacac-----------------------------------
---------------------aggaggagagcaagaatggttacaacccattccccggct
ttcgttacacgggatcattgagagcattccctgtgacaccaaagagag------------
-agatgcctgacacaatccctcgtcctgattatgctgaccattcagaaggtatccccctg
tcagagcgccagatgagaggttcgacaaacatcaaacagctggacgatgaagagcaggaa
ggcatgcgagttgtgtgcaagttggctcgggaaatcctggatgaagcagccagtgctgtt
ggcattggggtaactacagatgaaattgacagactggtccatgagg-caacaatagacag
agagtgctatccatctcct--------------------------------------ctg
aactactactgctt------tcccaaatcatgctgcac------------gtctatcaat
gaagtgat-------ctgtcatggaattcctgacacgaggccattggaa--aatggtgac
attgtcaacattgacat-----tactgcgtaccatcggagctttcatggggacttaaacg
agaccatgtttgtcggagaggtagacgaggccagcaaggagctggtgaagaccacacacg
aatgcctcatgatggccattgaagaagtgaaacccggtgtg-------------------
------------------------------------------------------------
------cggtacagagagatgggtaacatcatccagaag---catgcccaggcccatgga
tactctgttgttcgcagctactgtggccatggt--------------------------

```

**Code and output**:
### with awk

```awk
$ awk  '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}} END {print "\r"}' test.fa| awk -v RS='>' -v OFS="\n" '$2 ~ /[atgc]{180}/ {print ">"$1,$2}'

>Mitra_TRINITY_DN40971_c28_g2_i1
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------gtgctcttccgatctgccaaagaaacac--------------------------------------------------------aggaggagagcaagaatggttacaacccattccccggctttcgttacacgggatcattgagagcattccctgtgacaccaaagagag-------------agatgcctgacacaatccctcgtcctgattatgctgaccattcagaaggtatccccctgtcagagcgccagatgagaggttcgacaaacatcaaacagctggacgatgaagagcaggaaggcatgcgagttgtgtgcaagttggctcgggaaatcctggatgaagcagccagtgctgttggcattggggtaactacagatgaaattgacagactggtccatgagg-caacaatagacagagagtgctatccatctcct--------------------------------------ctgaactactactgctt------tcccaaatcatgctgcac------------gtctatcaatgaagtgat-------ctgtcatggaattcctgacacgaggccattggaa--aatggtgacattgtcaacattgacat-----tactgcgtaccatcggagctttcatggggacttaaacgagaccatgtttgtcggagaggtagacgaggccagcaaggagctggtgaagaccacacacgaatgcctcatgatggccattgaagaagtgaaacccggtgtg-------------------------------------------------------------------------------------cggtacagagagatgggtaacatcatccagaag---catgcccaggcccatggatactctgttgttcgcagctactgtggccatggt--------------------------
```

### with seqkit:

```awk
$ seqkit -w 0 grep -sdip "n{180}" test.fa

>Mitra_TRINITY_DN40971_c28_g2_i1
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------gtgctcttccgatctgccaaagaaacac--------------------------------------------------------aggaggagagcaagaatggttacaacccattccccggctttcgttacacgggatcattgagagcattccctgtgacaccaaagagag-------------agatgcctgacacaatccctcgtcctgattatgctgaccattcagaaggtatccccctgtcagagcgccagatgagaggttcgacaaacatcaaacagctggacgatgaagagcaggaaggcatgcgagttgtgtgcaagttggctcgggaaatcctggatgaagcagccagtgctgttggcattggggtaactacagatgaaattgacagactggtccatgagg-caacaatagacagagagtgctatccatctcct--------------------------------------ctgaactactactgctt------tcccaaatcatgctgcac------------gtctatcaatgaagtgat-------ctgtcatggaattcctgacacgaggccattggaa--aatggtgacattgtcaacattgacat-----tactgcgtaccatcggagctttcatggggacttaaacgagaccatgtttgtcggagaggtagacgaggccagcaaggagctggtgaagaccacacacgaatgcctcatgatggccattgaagaagtgaaacccggtgtg-------------------------------------------------------------------------------------cggtacagagagatgggtaacatcatccagaag---catgcccaggcccatggatactctgttgttcgcagctactgtggccatggt--------------------------

```