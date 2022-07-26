---
layout: post
title: Calculate GC content from multiline fasta with gaps in sequences
tags: python fasta multiline
---
One of the requests I came across to use basic python (without importing external libraries) for parsing multiline fastas with gaps in sequence. Example fasta is as follows:

```bash
>seq1
atgc
c t ga
>seq2
atcgc
actg
```
Here is the python code:

```python

import sys

output = []
input=sys.argv[1]
#input = "test.fa"

with open(input, "r") as w:
    seq = w.read().split(">")[1:]
    seq = [i.split("\n") for i in seq]

for i in seq:
    _id = i[0]
    _sequence = "".join(i[1:]).replace(" ","")
    gc_count = sum(map(_sequence.upper().count, ["G", "C"]))
    gc_perc = (gc_count/len(_sequence))*100
    _temp_ouput = [_id, len(_sequence), gc_perc]
    output.append(_temp_ouput)

[print(i) for i in output]

```

ouput from the code

```python
% ./test.py test.fa 

['seq1', 8, 50.0]
['seq2', 9, 55.55555555555556]
```
