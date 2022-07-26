---
layout: post
title: 'Locate if a base is present at the start of sequences in a list'
tags: python DNA biopython
---

One of the requests I got was to check if there is a base "A" at the start position given list of sequences. Sequence list follows as below

```bash
sequences = ["GAGGTAAACTCTG", "TCCGTAAGTTTTC", "CAGGTTGGAACTC","ACAGTCAGTTCAC", "TAGGTCATTACAG", "TAGGTACTGATGC"]

```
Now user wants to check if "A" is present at the start of each sequence and then count all such instances. Following are the ways to check if "A" is present at the start

```python
count = [int(i[0] == "A") for i in sequences]
# print(count)

count = []
for i in sequences:
    count.append(1 if i[0] == "A" else 0)
# print(count)

count = []
for i in sequences:
    count.append((0, 1)[i[0] == "A"])
# print(count)
```
But we would use only one for defining our function

```python
#!/usr/bin/env python3

sequences = ["GAGGTAAACTCTG", "TCCGTAAGTTTTC", "CAGGTTGGAACTC",
             "ACAGTCAGTTCAC", "TAGGTCATTACAG", "TAGGTACTGATGC"]


def count_first_base(sequences=sequences, base="A", pos=0):
    count = [int(i[pos] == base) for i in sequences]
    return [base, count, sum(count)]


print(*count_first_base(), sep="\t")    
print(base, *count_first_base(base="G", pos=2), sep="\t")
```
output is:

```bash
A       [0, 0, 0, 1, 0, 0]      1
G       [1, 0, 1, 0, 1, 1]      4

```