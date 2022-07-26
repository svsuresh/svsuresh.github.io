---
title: "Manipulate fasta headers using back referencing"
tags: [sed 'Fasta headers' seqkit 'back referencing']
layout: post
---

Biologists manipulate fasta headers for several reasons. Some of them are easy and some of them are complex. One of the user requirements to edit fasta headers and it involved back referencing. I did it using seqkit and sed.  Following is an example input:

```bash
input:

>16:23107820-23108019(+)
GTAC
>14:54909471-54909670(-)
GTAA
>7:127020805-127021004(-)
GTAGG
>X:20848619-20848818(+)
GTGAG
>1:75547398-75547597(+)
GTGGG
```
Now the requirement is to extract the coordinates from fasta headers including strand information.

```bash
with sed:

$ sed -n '/^>/ s/^>\(\w\+\)\W\(\w\+\)\W\(\w\+\).\([+,-]\)./\1\t\2\t\3\t\4/p' test.txt

16      23107820        23108019        +
14      54909471        54909670        -
7       127020805       127021004       -
X       20848619        20848818        +
1       75547398        75547597        +
```

NOw let us do the same thing using seqkit.

```bash
$ seqkit replace -ip '(\w+):(\w*)-(\w*).([+,-]).' -r '$1 $2 $3 $4' test.txt | seqkit seq -n

16 23107820 23108019 +
14 54909471 54909670 -
7 127020805 127021004 -
X 20848619 20848818 +
1 75547398 75547597 +
```
Good and interesting thing is that seqkit supports back referencing.  This can be used in multiple ways. In this case to split the header. Unfortunately, it doesn't support characters such **'\t'** (tab) in replacement.