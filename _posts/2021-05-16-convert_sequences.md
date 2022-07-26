---
layout: post
title: Extract nucleotide and amino acid sequences from genbank and ali 
tags: ali fasta awk genbank
---
In bioinformatics, it is common to extract information as there are multiple formats. First let us look at easy one. One of the requests was to extract the sequences from .ali into a fasta formatted sequence file. .ali files were taken from [here](https://biologydirect.biomedcentral.com/articles/10.1186/1745-6150-4-29#additional-information). I don't understand the format though it is named with .ali extension. In general, .ali extensions are for `PIR` format and the sequences were not in `PIR` format. Let us jump to the code. Here is the code for extracting sequences in .fasta format, from 13062_2009_161_MOESM2_ESM.ali.

```code
$ tr -s $'\t' < 13062_2009_161_MOESM2_ESM.ali | awk -v OFS="\n" '{print ">"$1,$2}' > 13062_2009_161_MOESM2_ESM.fa
```
Now let us get to genbank to fasta. Since genbank can have both nucleotide sequences and amino acid sequences (CDS), I wrote a code to extract both the sequences (Nucleotide and aminoacid) and is deposited in github. You can download it from [here](https://github.com/svsuresh/python_scripts/blob/13d1d66e046f3ae2e320e3a330376fda2e56aa12/gbtofasta/gbtofasta.py). Input file is hard coded and example files are included in the same directory as script. There is another use case where user wants to extract CDS sequence (amino acid), but wants to filter the sequence by a gene name, [here](https://github.com/svsuresh/python_scripts/blob/13d1d66e046f3ae2e320e3a330376fda2e56aa12/gbtofasta/gbk2seq.py) is the code to filter and extract sequence from genebank file. This script takes the user input for genbank and output file. If output file is name is not furnished, input extension is removed and `.fasta` extension will be appended. For eg `test.fasta` will be generated from `test.gbk`. Following is the usage.

```code
./gbk2seq.py input.gbk
./gbk2seq.py input.gbk output.fa
```
In fist case, output file name would be `input.fasta` and in later case, output would be `output.fa`