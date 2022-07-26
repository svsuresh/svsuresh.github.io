---
layout: post
title: Replace fasta headers using regex
tags: fasta seqkit headers
---
There are times, we want to replace sequence (in fasta format) headers using parts of the headers. Here is an example queestion and it's solution using seqkit.

Example input:
```text
>seq1 protein1
MEFKH
>seq2 protein 2
MKESR
>seq3 protein 3
MNNQR
```

User has another file with multicolumn mapping as below:

```
$ cat test.tsv
seq1	group_1000	ID_01
seq2	group_1001	ID_02
seq3	group_1002	ID_03      
```


Now user wants to append 3rd column (starting with ID)  at the after sequence ID (seq1,2,3), but retain rest of the text too. Expected output is:

```
>seq1 ID_01  protein1
MEFKH
>seq2 ID_02  protein 2
MKESR
>seq3 ID_03  protein 3
MNNQR  
```

Code for above problem is:

```bash
$ seqkit -w 0 --quiet replace -p '^(\w+)( .+)$' -r '${1} {kv} ${2}' -k <(awk -v OFS="\t" '{print $1,$3}'  test.tsv) test.fa
```