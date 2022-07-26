---
layout: post
title: 'Convert tab (tsv) file to fasta'
tags: fasta sed awk seqkit parallel shell bash
---

One of the easiest conversions is to convert a tab file to fasta. Reverse is also true except that some times sequences are in multiple lines and can cause issues in format conversion. 

Following is an example tab file to try on:
```code
seq1    atgc
seq2    gcta
seq3    agtcg
```
Let's list the methods to convert
```code
$ seqkit -w 0 tab2fx <input.fa> -o <output.fa(.gz)>
$ awk -v OFS="\n" '{print ">"$1,$2}' test.txt
$ awk -v OFS="\n" '{$1=">"$1}1' test.txt
$ sed  's/^/>/;s/\t/\n/' test.txt 
$ sed -r 's/^(.*)\t(.*)/>\1\n\2/' test.txt
$ parallel --colsep '\t' echo '\>'{1}'\\n'{2} :::: test.txt
$ while IFS=$'\t' read i j ; do echo ">"$i"\n"$j ; done < test.txt
```
