---
layout: post
title: Add count to duplicated IDs in fasta file
tags: awk python fasta
---

One of the common observation in fasta file is that some times headers (sequence Identifiers) are identical and are present multiple times. One of the requests was to append numbers starting from one to number of times that the header is identical. 

Here is an example of such file
```bash
$ cat gene.fa 

>geneA
atcc
>geneB
aaat
>geneB
aaat
>geneB
aaat
>geneC
atgg
>geneC
atgg
>geneD
atcg
```

AWK has awesome oneliner for this and it is copy/pasted from [here](https://www.unix.com/shell-programming-and-scripting/190813-using-awk-append-incremental-numbers-end-duplicate-file-names.html). I modified a little bit to apply only to fasta headers. Following is the awesome awk way:

```code
$ awk '/>/ {$1=$1"#"++a[$1]}1' gene.fa

>geneA#1
atcc
>geneB#1
aaat
>geneB#2
aaat
>geneB#3
aaat
>geneC#1
atgg
>geneC#2
atgg
>geneD#1
atcg

```

That being said, user wanted a python solution. Here is the python solution:

```python
#! /usr/bin/env python3
import os
from Bio import SeqIO
from Bio.SeqRecord import SeqRecord
import sys

input=sys.argv[1]
output=sys.argv[2]

rpid=""
num=1
new_seq=[]

for record in SeqIO.parse(input, "fasta"):
    if record.id == rpid:
        new_num+=num
    else:
        new_num=num
    newrecord_id='{0}#{1}'.format(record.id,new_num)
    new_seq.append(SeqRecord(record.seq,newrecord_id, description=""))
    rpid=record.id
SeqIO.write(new_seq, output, format="fasta")
```

Save this file as python script (for eg. append_duplicate_numbers.py), convert it to execute file (chmod +x append_duplicate_numbers.py) and run it following way:

```bash
./append_duplicate_numbers.py gene.fa out.fa
```

gene.fa is input and out.fa is output. Let us look at the output:

```bash
$ cat out.fa 

>geneA#1
atcc
>geneB#1
aaat
>geneB#2
aaat
>geneB#3
aaat
>geneC#1
atgg
>geneC#2
atgg
>geneD#1
atcggtc

```

My python skills are not as good as R skills. So please bear with me.