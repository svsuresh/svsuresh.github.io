---
title: Remove header information after space in fasta
tags: [fasta, awk, grep, sed]
layout: post
---
Here is an example of editing fasta headers with spaces. User wants to remove every thing after space in the header.

Example file

```bash
>ENST00000390567.1 cdna chromosome:GRCh38:14:105881034:105881053:-1 gene:ENSG00000211907.1 gene_biotype:IG_D_gene
GGTATAGTGGG
>ENST00000452198.1 cdna chromosome:GRCh38:14:105881539:105881556:-1 gene:ENSG00000225825.1 gene_biotype:IG_D_gene
GGGTATAGCA
```
Expected output:
```bash
>ENST00000390567.1 
GGTATAGTGGG
>ENST00000452198.1 
GGGTATAGCA
```
with grep:
```bash
$ grep -o "^\S*" test.fa

>ENST00000390567.1
GGTATAGTGGG
>ENST00000452198.1
GGGTATAGCA
```
$with Awk
```bash
awk -F " " '{print $1}' test.fa 

>ENST00000390567.1
GGTATAGTGGG
>ENST00000452198.1
GGGTATAGCA
```
with sed
```
$ sed '/^>/ s/\s.*//' test.fa 

>ENST00000390567.1
GGTATAGTGGG
>ENST00000452198.1
GGGTATAGCA
```
