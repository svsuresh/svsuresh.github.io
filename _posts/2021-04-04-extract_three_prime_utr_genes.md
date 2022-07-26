---
layout: post
title: "Extract genes and corresponsing transcripts with more than one 3' UTRs from a gtf"
tags: ["gff-utils", "gtf", "datamash", "awk", "UTR", "3' UTR", "three-prime-UTR"]
---

One of the requests I have come across is to extract genes with more than one 3' UTR exons. This is not tricky. However, scripts to do this are complex and gtf parsers are either half written or one has to write their own scripts to extract half tab separate file and Key-value (like dictionary in python) pair fields. Fortunately, I have come across a tool called [GFFutils](https://gffutils.readthedocs.io/en/latest/) that helps us to do this.

Please download the example gtf from [here](https://raw.githubusercontent.com/csoneson/jcc/master/inst/extdata/Homo_sapiens.GRCh38.90.chr22.gtf.gz). File is for chr22 only and it is around 100 kb. Idea here is to extract features of interest and then fields of interest, followed by grouping of data by genes, transcripts, followed by counting of 3'UTR feature, then filter the data where in count must be more than 1. Following is the code:

```bash
$ gtf_extract -f three_prime_utr --fields gene_id,gene_name,transcript_id,feature Homo_sapiens.GRCh38.90.chr22.gtf  | datamash -f -s -g2,3 count 3  | awk -v OFS="\t" 'NR ==1 {print "Gene_ID", "Gene_symbol","transcript", "3\047_UTR_Count"}; $5 >1 {print $1,$2,$3,$5}' 
```

Following is the partial output

```code
Gene_ID Gene_symbol transcript  3'_UTR_Count
ENSG00000283809 AC007326.4  ENST00000638240 2
ENSG00000099889 ARVCF   ENST00000263207 2
ENSG00000099968 BCL2L13 ENST00000399777 2
ENSG00000099968 BCL2L13 ENST00000498133 3
ENSG00000015475 BID ENST00000342111 3
ENSG00000242259 C22orf39    ENST00000509549 21
ENSG00000093009 CDC45   ENST00000263201 2
ENSG00000093009 CDC45   ENST00000404724 2
ENSG00000093009 CDC45   ENST00000407835 2
ENSG00000093009 CDC45   ENST00000428937 5
ENSG00000093009 CDC45   ENST00000437685 2
............
```