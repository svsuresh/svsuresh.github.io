---
layout: post
title: "Break multifasta into multiple files with awk"
tags: ["fasta", "awk"]
---

There are several ways to break a multifasta into small files. One of the requests I attempted is as follows:

```code
$ cat test.fa                                                                                                                                                                           
>LI5896452.1 Liverpool 2 kg/dp/Kng
ATGCTAG
>1582.LC madi kg 5/58/8
GATGAT
```
Requirement is to create individual files for each sequence and each file name should be the text till first space in the  headers (for eg. if the header is >LI5896452.1 Liverpool 2 kg/dp/Kng, file name should be LI5896452.1.fa). Here is the solution in awk.

```code
$ awk -v OFS="\n" '/^>/ {getline seq; print $0,seq > substr($1,2)".fa"}' test.fa  
```

Now user should see two files and the output would be:
```code
$ cat 1582.LC.fa                                                                                                                                                                        
>1582.LC madi kg 5/58/8
GATGAT

$ cat LI5896452.1.fa                                                                                                                                                                    
>LI5896452.1 Liverpool 2 kg/dp/Kng
ATGCTAG
```