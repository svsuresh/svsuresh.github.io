---
layout: post
title: "Extract sequences(lines) from multiline, multisequence fasta"
tags: awk fasta
---

One of the requests I got was to extract every 2 and 9 line from each multiline fasta sequence in a fasta file with multiple sequences. Follwing is the input example.

```code
$ cat test.fa

>seq1
line1
line2
line3
line4
line5
line6
line7
line8
line9
>seq2
sline1
sline2
sline3
sline4
sline5
sline6
sline7
sline8
sline9

```
code and output:

```awk
awk -v RS=">" -v OFS="\n" 'NR>1{print ">"$1,$3,$10}' test.fa
        
>seq1
line2
line9
>seq2
sline2
sline9

```

Now how about extracting only 2nd record (2nd fasta sequence from the list)

```bash
$ awk -v RS=">" -v OFS="\n" 'NR==3{print ">"$0}' test.fa            

>seq2
sline1
sline2
sline3
sline4
sline5
sline6
sline7
sline8
sline9

```

How about printing 2nd and 9th lines from sequence 2 in the multiline file

```bash
$ awk -v RS=">" -v OFS="\n" 'NR==3{print ">"$1,$3,$10}' test.fa       

>seq2
sline2
sline9

```

How about printing 2nd and 9 lines from selected records (here 1 and 2 as there are just records):

```bash

$ awk -v RS=">" -v OFS="\n" 'NR==3 || NR==2{print ">"$1,$3,$10}' test.fa

>seq1
line2
line9
>seq2
sline2
sline9
```

