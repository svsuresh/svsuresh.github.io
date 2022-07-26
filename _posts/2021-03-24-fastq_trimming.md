---
layout: post
title: Extract reads between primers including one side primer only
tags: cutadapt fastq
---

There are times that biologists may have to extract sequences between primers or motifs. For eg aptamer sequencing requires user to trim reads as per the sequences instead of adapters. Cutadapt has a provision to retain sequences used in sequencing. However, some times, one may need to retain one side of the sequence. Following is example and solution (taken from biostars).

Example fastq

```code
@A00877:568:HVV57DSXY:4:1101:27724:1408 1:N:0
GGGTCAGTAGCGGACGGGAACTGCATCACGCAATACGACTCACTATAGGGTCAGTAGCGGAC
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
@A00877:568:HVV57DSXY:4:1102:27724:1408 1:N:0
GGGTCAGTAGCGGACGGGAAACGTCGCACGCAATACGACTCACTATAGGGTCAGTAGCGGAC
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
@A00877:568:HVV57DSXY:4:1103:27724:1408 1:N:0
GGGTCAGTAGCGGACGGGAACTGCACGTCAGCTGGCGACTCACTATAGGGTCAGTAGCGGAT
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```

Now the requirement is to extract read sequences between primers **GGGTCAGTAGCGGAC** and **GGGTCAGTAGCGGAC**. But retain 3' primer sequence. Following is the solution

```code
$ cutadapt -e 0  -g GGGTCAGTAGCGGAC...GGGTCAGTAGCGGAC  --action=retain --discard-untrimmed  --quiet test.fq | cutadapt -u -17 - --quiet

@A00877:568:HVV57DSXY:4:1101:27724:1408 1:N:0
GGGTCAGTAGCGGACGGGAACTGCATCACGCAATACGACTCACTA
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
@A00877:568:HVV57DSXY:4:1102:27724:1408 1:N:0
GGGTCAGTAGCGGACGGGAAACGTCGCACGCAATACGACTCACTA
+
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```
-e 0 is for zero errors in matching the primer sequence. Rest parameters are standard parameters.
